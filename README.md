# Oathkeeper, Kratos and Keto example

This repository show how create an SSO and ACL system with the Ory stack and Kubernetes.

## How to run

Edit [env/default.yaml](env/default.yaml):

```bash
$ helmfile --file helmfile.yaml --namespace ory apply --wait --wait-for-jobs

# fix ingress 
$ kubectl patch ing/keto-read --type=json --namespace ory -p='[{"op": "replace", "path": "/spec/rules/0/http/paths/0/backend/service/name", "value":"keto-read"}]'
$ kubectl patch ing/keto-write --type=json --namespace ory -p='[{"op": "replace", "path": "/spec/rules/0/http/paths/0/backend/service/name", "value":"keto-write"}]'

### brew install httpie
$ http PUT http://keto.example.com/write/relation-tuples namespace=access object=administration relation=access subject_id=admin
$ http PUT http://keto.example.com/write/relation-tuples namespace=access object=application relation=access subject_id=admin
$ http PUT http://keto.example.com/write/relation-tuples namespace=access object=application relation=access subject_id=user

# check
$ http -b http://keto.example.com/read/check namespace=access object=administration relation=access subject_id=admin
{
    "allowed": true
}
```

## How to use

Go to http://ory.example.com/panel/ and create an account, you can validate your mail on http://mail.example.com. When you create an account you have to role `user` or `admin`. Only the admin role have the right to access the protected page.

| URL | Description |
| :--- | :--- |
| http://ory.example.com/panel/welcome | User app for create an account, login, other |
| http://ory.example.com/admin/test | Protected page -> https://httpbin.org/anything/test |
