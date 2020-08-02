testing out openfaas templates

```sh
faas-cli template pull https://github.com/pyramation/python-template-openfaas-of-watchdog-issue
```

list available templates

```sh
faas-cli new --list
```

create a new JS function for jobs

```sh
faas-cli new --lang python2.7 my-new-py-function
faas-cli new --lang python3 my-new-py-function
```
