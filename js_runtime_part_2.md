# How to make a JavaScript Runtime from scratch: Part 2 
By Hao Tran

## Preface 

In the previous article we talked about the code requried to run vanilla JavaScript with V8. In this article, we will be exploring 
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
## Main Ideas 
1. ** **

`execute_extended_javascript.rs`

``` rust
use rusty_v8 as v8; 

fn main(){

//INITIALIZE V8
let platform = v8::new_default_platform(0, false).make_shared();
v8::V8::initialize_platform(platform);
v8::V8::initialize();

let isolate = &mut v8::Isolate::new(Default::default()); 
let handle_scope = &mut v8::HandleScope::new(isolate);
let context: v8::Local<v8::Context> = v8::Context::new(handle_scope);
let scope = &mut v8::ContextScope::new(handle_scope, context);

//READ FILE 
let filepath: &str = "hello_world.js"; 
let file_contents = match read_file(filepath){
    Ok(contents) => contents, 
    Err (e) => {
        eprintln!("ERROR: {}", e);
        return; 
    }
};

//BIND CONSOLE TO GLOBAL
let console = v8::Object::new(scope);
let function_template_console = v8::FunctionTemplate::new(scope, console_log_callback);
let log_function = function_template_console.get_function(scope).unwrap();

let key = v8::String::new(scope, "log").unwrap(); 
console.set(scope, key.into(), log_function.into());

let console_key = v8::String::new(scope, "console").unwrap();
global.set(scope, console_key.into(), console.into());

//EXECUTE CODE
let code = v8::String::new(scope, &file_contents).unwrap(); 
let script = v8::Script::compile(scope, code, None).unwrap();
let result = script.run(scope).unwrap();
let result = result.to_string(scope).unwrap();
println!("Results: {}", result.to_rust_string_lossy(scope));

}

//DEFINE CALLBACK
fn console_log_callback(
handle_scope: &mut v8::HandleScope, 
args: v8::FunctionCallbackArguments, 
_return_object: v8::ReturnValue 
){

    //convert from V8 string local handle to Rust String
    let input_str = args
        .get(0) 
        .to_string(handle_scope)
        .unwrap()
        .to_rust_string_lossy(handle_scope);

    println!("{}", input_str);
}
```

Note that this program now includes two additional sections `\\BIND CONSOLE TO GLOBAL` and `DEFINE CALLBACK`. 