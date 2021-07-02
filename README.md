# Kubernetes on a Rasperry Pi Cluster

## Prepare the SD Cards

I use the [Raspberry Pi Imager](https://www.raspberrypi.org/software/) to install Ubuntu Server on the SD Card

### Startup configuration

1. [Enable SSH](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) (Solution #3)
2. [Setup Wifi](https://discourse.ubuntu.com/t/how-to-install-ubuntu-server-on-your-raspberry-pi/14660)

### Intial doings

1. Get the IP address and configure the host in the [`ansible/hosts.yaml`](ansible/hosts.yaml)
2. Execute the ansible Playbook `ansible-playbook playbook.yaml -i hosts.yaml`
3. Do the Flux bootstrap:
    ```shell
   export GITHUB_TOKEN=$(cat ~/.github-token)
   flux bootstrap github \
    --owner=gleichda \
    --repository=raspberry-kubernetes \
    --path=gitops \
    --branch=main \
    --commit-message-appendix='[FLUX]' \
    --ssh-key-algorithm=ed25519 \
    --personal
   ```
4. Get the Kube Config from the api server and add it to your ~/.kube/config:
   ```shell
   ssh <HOST> sudo cat /etc/kubernetes/admin.conf > config
   KUBECONFIG=~/.kube/config:$PWD/config kubectl config view --flatten > ~/.kube/config
   ```


## To be Automated stuff

IP MAsq: apply the `[IP Masq agent](./gitops/kube-system/ip-masq-agent.yaml)`
