##server
```
import (
	"log"
	"fmt"

	"net/http"
	"io/ioutil"
	"net/url"
)

func main() {
	http.HandleFunc("/test", IndexHandler)
	err:=http.ListenAndServe("127.0.0.1:8070", nil)
	log.Println(err)
}

func IndexHandler(w http.ResponseWriter, r *http.Request) {
	log.Println("recv msg")
	s, _ := ioutil.ReadAll(r.Body) //把  body 内容读入字符串 s
	if len(r.Header) > 0 {
		for k,v := range r.Header {
			log.Printf("%s=%s\n", k, v[0])
		}
	}
	log.Println("打印Form参数列表：")
	r.ParseForm()
	if len(r.Form) > 0 {
		for k,v := range r.Form {
			log.Printf("%s=%s\n", k, v[0])
		}
	}
	urlParam := r.URL.RawQuery
	log.Println(urlParam)
	log.Println(url.ParseQuery(urlParam))
	log.Println(string(s))
	fmt.Fprintln(w, "hello world")
}
```

##client
```
func post(){

	timestamp:=strconv.Itoa(int(time.Now().UnixNano()/1e9))
	signature:=app_code+timestamp+app_token
	
	data := []byte(signature)
	has := md5.Sum(data)
	md5str1 := fmt.Sprintf("%x", has)
	
	content:= test("1",[]string{"2","3"},"hello")
#构建 url parameters
	info := url.Values{}
	info.Add("app_code",app_code)
	info.Add("signature",md5str1)
	info.Add("timestamp",timestamp)
	info.Add("content",content)
	d:=info.Encode()
	log.Println(d)
	url_str=url_str+"?"+d

	log.Println(url_str)
	
	//reader := bytes.NewReader(bytesData)
	request, err := http.NewRequest("POST", url_str,nil)
	if err != nil {
		fmt.Println(err.Error())
		return
	}
	request.Header.Set("Content-Type", "application/json;charset=UTF-8")
	client := http.Client{}
	resp, err := client.Do(request)
	if err != nil {
		fmt.Println(err.Error())
		return
	}
	respBytes, err := ioutil.ReadAll(resp.Body)
	if err != nil {
		fmt.Println(err.Error())
		return
	}
	//byte数组直接转成string，优化内存
	str := (*string)(unsafe.Pointer(&respBytes))
	fmt.Println(*str)
}

```