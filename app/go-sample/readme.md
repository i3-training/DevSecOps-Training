# Go REST API Sample app

## API

API di expose dengan port `10000` Mengakses API dapat melalui `localhost:10000` atau ip dari server, daftar dibawah menunjukan endpoint yang dapat di akses oleh user.

#### http://localhosts:10000/
* `GET` : Get Home Page

#### http://localhosts:10000/articles
* `GET` : Get all articles

#### http://localhosts:10000/article
* `POST` : Create a new articles

#### http://localhosts:10000/article/{id}
* `GET` : Get an article
* `DELETE` : Delete a articles

### JSON structure

```json
[
  {
    "Id": "1",
    "Title": "Hello",
    "desc": "Article Description",
    "content": "Article Content"
  },
  {
    "Id": "2",
    "Title": "Hello 2",
    "desc": "Article Description",
    "content": "Article Content"
  }
]
```

#### Create new entry using POST

```json
{
    "Id": "3", 
    "Title": "Newly Created Post", 
    "desc": "The description for my new post", 
    "content": "my articles content" 
}
```

## Run App

Untuk menjalankan aplikasi di local system, opsi pertama menjalankan source langsung dengan `go run`, opsi kedua dengan build source dan run source tersebut dengan perintah `go build` kemudian run source tersebut dengan command `./binary`

```bash
go run ./main.go

go build -o rest-app ./main.go
./rest-app
```
