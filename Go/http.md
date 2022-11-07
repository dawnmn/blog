```
func main()  {
 http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("hello"))
 })
 http.ListenAndServe("localhost:8000", nil)
}
```
```
func main()  {
   http.ListenAndServe("localhost:8000", server{})
}
type server struct {}
func (s server) ServeHTTP(w http.ResponseWriter, r *http.Request) {
   switch r.RequestURI {
   case "/":
      w.Write([]byte("home"))
   case "/joke":
      w.Write([]byte("joke"))
   }
}
```
```
func main()  {
   mux := http.NewServeMux()
   mux.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
      w.Write([]byte("home"))
   })
   mux.HandleFunc("/joke", func(w http.ResponseWriter, r *http.Request) {
      w.Write([]byte("joke"))
   })
   http.ListenAndServe("localhost:8000", mux)
}
```