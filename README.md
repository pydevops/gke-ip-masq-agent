## Objective
Want to apply the masquerade rules so that pod can communicate to other private network in GCP over VPN or VPC peering? 

[Enable Network policy](https://cloud.google.com/kubernetes-engine/docs/how-to/network-policy#enabling_network_policy_enforcement) is usually required for ip-masquerade-agent on GKE. 

I figured out how to add this on any existing GKE cluster regardless of any network policy enabled or not. 

## Reference
* https://cloud.google.com/kubernetes-engine/docs/how-to/ip-masquerade-agent


## Steps
For example, assuming 10.4.0.0/20 is the VPN cidr range the pods want to communicate with.

1. Add the 10.4.0.0/20 under  **masqueradeCIDRs** in **cm-ip-masq-agent.yaml**
2. Next we need to build our custom ip-masq-agent docker image from https://github.com/vdavidoff/ip-masq-agent, the pull request hasn't been approved in the original repo. The pull request can enable adding masquerading CIDR range. That is exactly what we needed for pods talking VPN or VPC peering. 
3. Modify **ds-ip-masq-agent.yaml** for the custom image url such as image: `gcr.io/<project_id>/ip-masq-agent-amd64:0.0.1`
4. Finally
```
kubectl apply -f ds-ip-masq-agent.yaml
kubectl apply -f cm-ip-masq-agent.yaml
```