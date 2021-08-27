# PoC for kops cluster-base/addons deployment

## Using this PoC
1. Deploy a naked kops cluster (with networking set to cni):
```shell
kops create cluster --zones=eu-central-1a test.clusters.kluctl.io --networking cni
kops update cluster --name test.clusters.kluctl.io --yes --admin
```

Now wait...

2. Update `clusters/` directory with your own cluster (e.g. copy test.clusters.kluctl.io.yml and replace cluster and context names)
3. Use kluctl to manage addons:
```
# will initially complain about missing namespaces
kluctl diff --cluster test.clusters.kluctl.io

# do the initial deployment
kluctl deploy --cluster test.clusters.kluctl.io
```
4. Now pretend that we upgrade/modify some addons
```
# modify something, e.g. enable/disable hubble-ui (in clusters/test.clusters.kluctl.io.yml)

# Do a diff to check what would change
kluctl diff --cluster test.clusters.kluctl.io

# Let kluctl deploy everything and then tell you what actually changed
kluctl deploy --cluster test.clusters.kluctl.io

# In case stuff got deleted, run purge
kluctl purge --cluster test.clusters.kluctl.io
```
5. Try to enable the guestbook application by commenting it in inside the root `deployment.yml` and retry 4. This
should of course not be part of a kops addons deployment, but it shows (in a very simplified/naive way) how
users can later deploy their own applications (in case they decide to use kluctl as well).
6. Integrate this into kops and let it happen magically :)

## Further infos/documentation

I've put some comments into the deployment.yml other files. I'd also recommend to read the documentation found
[here](https://github.com/codablock/kluctl).

The way kluctl is used in this PoC differs a little bit in what is described/recommended in the docs, 
as we do not use a .kluctl.yml here but instead provide the cluster name directly via arguments.

## Helm charts used here

I've already prepared everything what is needed to use the Helm Charts (cert-manager, metrics-server, cilium)
involved here. If you'd like to change chart versions, you'll have to call `kluctl helm-pull` after changing
the `helm-chart.yml` files.
