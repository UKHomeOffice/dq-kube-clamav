# dq-clamav
This is customised DQ clamav Service that is based on the ACP Clamav service https://github.com/UKHomeOffice/clamav-http. Dq-clamav has repalced the clamav sidekick scanner that is deployed in the OAG, NATS and ACL ingest pods which ACP have stopped supporting.

## Components

clamav-http is made up of four components, clamav, clamav-http, clamav-notify and clamav-notify-cron and is designed to be deployed as a service in kubernetes via its helm chart. for more information about these components and how they fit together please refer to https://github.com/UKHomeOffice/clamav-http

## Dependencies

- Docker
- Drone 1.0
- helm
- Kubernetes


## Drone secrets

Environmental variables are set in Drone based on secrets listed in the *.drone.yml* file and they are passed to Kubernetes as required.                                                        

## Installation

Basic installation can be acheived by running:

```
helm install -n <namespace> clamav ./charts/clamav
```

Clamav will be installed in the namespace and available at https://clamav/

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
