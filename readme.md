# simple python server request body empty when using of-watchdog 

## Expected Behaviour

when `POST`ing a request to a cloud funtion, the request body should be available inside the cloud function. Specifically, the header for `content-length` should be set and the data should be readable. 

We should expect, for example, 

```
curl --header "Content-Type: application/json" \
  --request POST \
  --data '{"a":"1","b":"2"}' \
http://localhost:31112/function/py2fn.openfaas-fn
```

to repond with:

```
{"function": "ok", "params": {"a": "1", "b": "2"} }
```

## Current Behaviour

Currently the response is missing the body completely. When I check content length it is not > 0, hence the result:

```
{"function": "ok", "params": {} }
```

## Steps to Reproduce (for bugs)

1. pull the template

```sh
faas-cli template pull https://github.com/pyramation/python-template-openfaas-of-watchdog-issue
```

2. create a new functions

```sh
faas-cli new --lang python2.7 py2fn
faas-cli new --lang python3 py3fn
```

3. build and deploy

Since the functions are just passing through params, we can build and deploy them immediately:

```sh
export OPENFAAS_URL=localhost:31112
faas build -f ./py2fn.yml
faas build -f ./py3fn.yml
faas deploy -f ./py2fn.yml
faas deploy -f ./py3fn.yml
```

4. make requests and see both have same result

```sh
curl --header "Content-Type: application/json"   --request POST   --data '{"a":"1","b":"2"}' http://localhost:31112/function/py2fn.openfaas-fn
> {"function": "ok", "params": {} }

curl --header "Content-Type: application/json"   --request POST   --data '{"a":"1","b":"2"}' http://localhost:31112/function/py3fn.openfaas-fn
> {"function": "ok", "params": {} }
```

5. verify that it works locally, with proper responses

For python 2.7

```sh
cd ./build/py2fn
touch function/__init__.py && PORT=10101 python index.py

# now on another shell...
curl --header "Content-Type: application/json"   --request POST   --data '{"a":"1","b":"2"}' http://localhost:10101
> {"function": "ok", "params": {"a": "1", "b": "2"}}
```

For python 3

```sh
cd ./build/py3fn
touch function/__init__.py && PORT=10101 python3 index.py

# now on another shell...
curl --header "Content-Type: application/json"   --request POST   --data '{"a":"1","b":"2"}' http://localhost:10101
> {"function": "ok", "params": {"a": "1", "b": "2"}}
```

## Context

Trying to create languages for of-watchdog, so I can manipulate headers and create a connector that I'm working on in postgres. Currently nodejs works, so I'm a bit baffled by this body parsing issue.

## Your Environment
* Docker version 19.03.12
* FaaS-netes
* MacOS
* https://github.com/pyramation/python-template-openfaas-of-watchdog-issue


