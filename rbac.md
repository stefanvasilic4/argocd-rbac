## ArgoCD RBAC

- When use SSO Openshift is not allowing to sync App, but it works when logged in with ArgoCD

~~
rbac:
  defaultPolicy: ''
  policy: |
    g, system:cluster-admins, role:admin
  scopes: '[name,groups]'                        <<<< add name
~~~

To create local users:

- if ArgoCD is deployed with `openshift-gitops` operator it gets tricky because `argocd-cm` is controlled by the operator but `data.accounts` is not. However we is not recommended using local user but DEX SSO integrated users

~~~
oc edit cm argocd-cm -n openshift-gitops
data:
accounts.john: apiKey,login  // <===== Add this line
~~~

`argocd account list`

- Create Password for User
Raw

`argocd account update-password --account john`

-----------------------------------

~~~
rbac:
  defaultPolicy: ''
  policy: |
    g, system:cluster-admins, role:admin
    g, cluster-admins, role:admin
    g, developer, role:developer
    g, marketing, role:marketing
  scopes: '[groups]'                       
~~~

- Create a cluster-admins group.

`oc adm groups new cluster-admins`

- Bind the group to the cluster-admin role.

`oc adm policy add-cluster-role-to-group cluster-admin cluster-admins`

- To add a set of users to the cluster-admins group :

`oc adm groups add-users cluster-admins admin`
