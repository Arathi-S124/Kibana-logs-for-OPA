STEP 5:- Apply OPA Policy in the Namespace

Make sure you have Gatekeeper up and running, if not begin by doing

```
kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/release-3.3/deploy/gatekeeper.yaml
```

We can make use of the usecases for the following process -> [OPA Usecases](https://github.com/Arathi-S124/Kibana-logs-for-OPA/tree/master/STEP%205/OPA%20Usecases)

1. You’ll need to save 2 files - Constraint and Template.. Constraint.yaml file shall help initiating the policy library and Template.yaml is the file where we have made our violation policy for the required usecases.
2. Next once you have these files apply them using :-

```
kubectl apply -f constraint.yaml
```
```
kubectl apply -f template.yaml
```
3. Now make use of deployment-violation.yaml so that we can get error logs because of policy for violation we applied above, deployment-non-violation.yaml files shall help you get successful pod creation. 
4. From the Discover page on your Kibana setup, in the search bar enter options like OPA, error and we shall have filtered the log data for pods that were used to test OPA usecases.

Learn about OPA Scenarios in detail at [Mastering OPA Policies](https://cloudsecops.com/opa-gatekeeper)

