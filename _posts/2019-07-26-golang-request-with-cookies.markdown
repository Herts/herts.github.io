---
layout: post
title: "Golang request with cookies"
date: "2019-07-26 05:48:09 -0700"
---
We should get an instance of ``http.Client`` and assign a cookiejar to ``client.Jar``.
```Go
func login() *http.Client {
	cookieJar, _ := cookiejar.New(nil)
	client := &http.Client{
		Jar: cookieJar,
	}
	urlData := url.Values{}
	loginUrl := "http://www.example.com/accounts/login"

	tokenResp, _ := soup.GetWithClient(loginUrl, client)
	tokenHtml := soup.HTMLParse(tokenResp)
	token := tokenHtml.Find("input", "name", "csrfmiddlewaretoken")

	urlData.Set("username", "user123")
	urlData.Set("password", "123456")
	urlData.Set("csrfmiddlewaretoken", token.Attrs()["value"])
	_, err := client.PostForm(loginUrl, urlData)
	if err != nil {
		panic(err)
	}
	return client
}

``
