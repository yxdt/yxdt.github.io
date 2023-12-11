---
layout: post
title: Ajax in vanilla javascript
date: 2023-12-05 18:37:29 - 0800
categories: [frontend, javascript]
tag: [frontend]
---

# {{page.title}}

## The request

We are dealing with a dynamic data sharing and displaying scenairo via http to mobile users, the quick response speed is essential. So we have to keep the html file as small as possible. And this one single web page contains skeleton component, the data request and display logic and even user permission verifications. All dynamic data request will send to server via Ajax.

## What is Ajax

Ajax (Asynchronous JavaScript and XML) is a technique used in web development to send and receive data from a server without having to reload the entire web page. It allows for asynchronous communication between the client-side and server-side, enabling dynamic content updates.

## Major Ajax npm package & libraries

1. axios 
2. jQuery ajax 

These Ajax libs are either big or too complex. What I need here is just a simple fetch function retriving some data from remote server. So we can start from scratch in vanilla javascript:

## Vanilla javascript Ajax code snippet

The key concept is using XMLHttpRequest object, we can easily get remote data:

```javascript
function handleResponse(xhr) {
    if(xhr.readyState === 4 ){
        // Request completed
        if(xhr.status === 200) {
            const jsonData = JSON.parse(xhr.responseText);

            //do the data formatting and other logic...
            ...
        } else {
            // Request failed
        }
    }
}

const xhr = new XMLHttpRequest();

// we can handle either success or failed request
xhr.onreadystatechange = () => handleResponse(xhr);
//
//param: method： "GET" OR "POST"
//param: url: 
//param: async :true (default), sync: false
//
xhr.open("GET", `${baseUrl}api/request?id=${requestId}`, true);
xhr.setRequestHeader('Content-Type', 'application/json');
xhr.send();
```

## Wrap it with promise for code re-using

If the whole page is only one data request, the code snippets above will do the job, however, there are several requests in one page, so we need to re-use the code for each remote request. 

By using promise, we can wrap the ajax code into a async function:  

```typescript
type RequestParams {
    url: string,
    method?: 'GET'|'POST', //or others    
    headers?: {
        [key: string] : string|number
    }
}
function ajaxRequest({
    url, 
    method = 'GET',     
    headers = {}} : RequestParams) : Promise {
    return new Promise((resolve, reject) => {
        const xhr = new XMLHttpRequest();

        xhr.open(method, url, true);
        for(const [key, value] of Object.entries(headers)){
            xhr.setRequestHeader(key, value);
        }
        xhr.onreadystatechange = () => {
            if(xhr.readyState === 4) {
                //Request Complete
                if(xhr.status === 200) {
                    //success
                    ...
                    //send the data back to caller via resolve 
                    resolve(xhr.responseText)
                } else {
                    //request failed
                    ...
                    //send the message back to caller via reject 
                    reject({error: 'Request failed', method, url, headers})
                }
            }
        }
        
        xhr.send();
    })
}

//How we use it
try{
    const theData = await ajaxRequest({url:`${baseUrl}api/request?id=${reqId}`})
    const formatted = JSON.parse(theData)
    //... do the rest
} catch(error) {
    //handle errors
}
```


## make it in seperate file 

We can also store the code into seperate js file 

```typescript
//ajaxrequest.ts

export function ajaxRequest() {
    //see the code in previous sections
    ...
}


// in caller ts files:
import {ajaxRequest}  from 'ajaxrequest'

try {
    const theData = await ajaxRequest(...)
    ...
} catch (error) {
    ...
}

```

## what can be done next?

1. we can wrap and make a npm package and release it to public 
2. write tests for the code to cover all scenarios
