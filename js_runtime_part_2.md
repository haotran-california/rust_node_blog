##How to make a JavaScript Runtime from scratch: Part 2 

In our previous article we talked about the code requried to run vanilla JavaScript with V8. In this article, we will be exploring 
the code required to extend JavaScript beyond ECMAScript. 

to run code which is provided by runtimes like Node.js  


Here is Eric Wendal's definition of a JavaScript Runtime. 

You can checkout his YouTube video on how to make Node.js from scratch using C++. He provides a Gitpod enviorment which has V8 and Libuv setup out the box. 


Here is an example of code which can currently be executed by V8 and cannot. 

```javascript 
function addTwoNumbers(a, b){
    return (a + b);
}

addTwoNumbers(1+1);

```



```javascript 
console.log("Hello World");

```

