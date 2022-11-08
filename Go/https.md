**生成证书**
cert.go
```
package main

import (
   "crypto/ecdsa"
   "crypto/elliptic"
   "crypto/rand"
   "crypto/x509"
   "crypto/x509/pkix"
   "encoding/pem"
   "log"
   "math/big"
   "os"
   "time"
)

func generate(dnsNames []string) {
   privateKey, err := ecdsa.GenerateKey(elliptic.P256(), rand.Reader)
   if err != nil {
      log.Fatalf("Failed to generate private key: %v", err)
   }

   serialNumberLimit := new(big.Int).Lsh(big.NewInt(1), 128)
   serialNumber, err := rand.Int(rand.Reader, serialNumberLimit)
   if err != nil {
      log.Fatalf("Failed to generate serial number: %v", err)
   }

   template := x509.Certificate{
      SerialNumber: serialNumber,
      Subject: pkix.Name{
         Organization: []string{"公司/机构名称"},
      },
      DNSNames:  dnsNames,
      NotBefore: time.Now(),
      NotAfter:  time.Now().Add(time.Hour * 24 * 365 * 3),

      KeyUsage:              x509.KeyUsageDigitalSignature,
      ExtKeyUsage:           []x509.ExtKeyUsage{x509.ExtKeyUsageServerAuth},
      BasicConstraintsValid: true,
   }

   derBytes, err := x509.CreateCertificate(rand.Reader, &template, &template, &privateKey.PublicKey, privateKey)
   if err != nil {
      log.Fatalf("Failed to create certificate: %v", err)
   }

   pemCert := pem.EncodeToMemory(&pem.Block{Type: "CERTIFICATE", Bytes: derBytes})
   if pemCert == nil {
      log.Fatal("Failed to encode certificate to PEM")
   }
   if err := os.WriteFile("cert.pem", pemCert, 0644); err != nil {
      log.Fatal(err)
   }
   log.Print("wrote cert.pem\n")

   privBytes, err := x509.MarshalPKCS8PrivateKey(privateKey)
   if err != nil {
      log.Fatalf("Unable to marshal private key: %v", err)
   }
   pemKey := pem.EncodeToMemory(&pem.Block{Type: "PRIVATE KEY", Bytes: privBytes})
   if pemKey == nil {
      log.Fatal("Failed to encode key to PEM")
   }
   if err := os.WriteFile("key.pem", pemKey, 0600); err != nil {
      log.Fatal(err)
   }
   log.Print("wrote key.pem\n")
}

func main() {
   generate([]string{"test"})
}
```
linux中查看证书 `openssl x509 -in cert.pem -text`
hosts配置域名解析 `192.168.2.64 test`

**https服务器**
```
package main

import (
   "crypto/tls"
   "fmt"
   "log"
   "net/http"
)

func main() {
   mux := http.NewServeMux()
   mux.HandleFunc("/", func(w http.ResponseWriter, req *http.Request) {
      if req.URL.Path != "/" {
         http.NotFound(w, req)
         return
      }
      fmt.Fprintf(w, "Proudly served with Go and HTTPS!")
   })

   srv := &http.Server{
      Addr:    "0.0.0.0:4000",
      Handler: mux,
      TLSConfig: &tls.Config{
         MinVersion:               tls.VersionTLS13,
         PreferServerCipherSuites: true,
      },
   }

   log.Printf("Starting server")
   err := srv.ListenAndServeTLS("./cert.pem", "./key.pem")
   log.Fatal(err)
}
```
**http客户端**
```
package main

import (
   "crypto/tls"
   "crypto/x509"
   "fmt"
   "io"
   "log"
   "net/http"
   "os"
)

func main() {
   cert, err := os.ReadFile("./cert.pem")
   if err != nil {
      log.Fatal(err)
   }
   certPool := x509.NewCertPool()
   if ok := certPool.AppendCertsFromPEM(cert); !ok {
      log.Fatalf("unable to parse cert")
   }

   client := &http.Client{
      Transport: &http.Transport{
         TLSClientConfig: &tls.Config{
            RootCAs: certPool,
         },
      },
   }

   r, err := client.Get("https://test:4000")
   if err != nil {
      log.Fatal(err)
   }
   defer r.Body.Close()

   html, err := io.ReadAll(r.Body)
   if err != nil {
      log.Fatal(err)
   }
   fmt.Printf("%v\n", r.Status)
   fmt.Printf(string(html))
}
```