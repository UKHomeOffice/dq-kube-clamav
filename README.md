# dq-clamav
This is customised DQ clamav Service that is based on the ACP Clamav service https://github.com/UKHomeOffice/clamav-http. Dq-clamav has replaced the clamav sidekick container that is deployed in the OAG, NATS and ACL ingest pods which ACP have stopped supporting.

## Components

clamav-http is made up of two components, clamav and clamav-mirror and is designed to be deployed as a service in kubernetes via its helm chart.

There are 2 Clamav Pods in each namespace (Dev, Notprod & Prod)
- Clamav Pod which contains
  - Clamav container which includes both Clamd virus scanner and Freshclam* running as daemon
  - clamav-http - RESP API endpoint to expose clamd in Clamav container making it reachable remotely
  - Nginx endpoint through which the REST APi is exposed on the pod port TCP 443 to encrypt virus scan requests
- Clamav Mirror pod
  - is a k8s StatefulSet pod(s) that has Freshclam which connects to the clamav virus definition servers on a regular interval to obtain the virus definition updates   

## Dependencies

- Docker
- Drone
- helm
- Kubernetes


## Drone secrets

Environmental variables are set in Drone based on secrets listed in the *.drone.yml* file and they are passed to Kubernetes as required.                                                        

## Installation

Basic installation can be acheived by running:

```
helm install -n <namespace> clamav ./charts/clamav
```

Clamav will be installed in the namespace and available at https://dq-clamav/

More detailed documentation on the helm chart can be found [here](/charts/clamav/README.md)

## Scanning Files using dq-clamav

The dq-clamav endpoint can be called from any pod that is deployed in the same namespace as the dq-clamav pod.

To scan a file using the dq-clamav service:
- Run the following command from container (provioded that curl is insralled)

```
curl -sk -F "name=eicar" -F "file=@sample.xml" https://dq-clamav/scan
```

- Use the python3 module requests as follows:

```
response = requests.post('https://dq-clamav/scan', files={'file': scan}, data={'name': scan_file}, verify=False)
```
