# workadventure - kustomize deployment

## Deploy


1. First deploy traefik with a tls challenge resolver. If you are using k3s, you should disable the default traefik, and install traefik with k3s-infra manifests.

2. Copy/edit the overlay directory.

3. Copy the `.env.template` and edit it.

    cp overlays/workadventure.suse.net/.env{.template,}

4. Test output

    kustomize build overlays/workadventure.suse.net

4. Deploy (kustomize version in kubectl is too old, so pipe to appl):

    kustomize build overlays/workadventure.suse.net | kubectl apply -f -
