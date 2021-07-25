
A new member akshay joined our team.
He requires access to our cluster. The Certificate Signing Request is at the /root location.
Create a CertificateSigningRequest object with the name akshay with the contents of the `akshay.csr` file

```shell script
# first you have to create a definition file for the Cerfificate Signing Request.
# In the request. The request must be encoded in base 64 format

cat .csr | base64


master $ kubectl create -f akshay.yaml
certificatesigningrequest.certificates.k8s.io/akshay created
```


```yaml
apiVersion: certificates.k8s.io/v1beta1
kind: CertificateSigningRequest
metadata:
  name: akshay
spec:
  groups:
  - system:authenticated
  request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZqQ0NBVDRDQVFBd0VURVBN
QTBHQTFVRUF3d0dZV3R6YUdGNU1JSUJJakFOQmdrcWhraUc5dzBCQVFFRgpBQU9DQVE4QU1JSUJD
Z0tDQVFFQXdIcEJWc2JvSnlRMXMyV3NvaVBoNGtUN1JFV2JDclNwMmt1Ui82NkZXSUlHCituMDk5
MnZ0dDN0WkJFWUpXOWlFV1AwYTltbXVXUWJxK0JhZGhLd3R6aVVKTDJVVjFzUDZ5NlJIc2s2RzlQ
RnYKRFIvaTA2eGt4RlFhS2IrU2QzT3lnOVkyWDlvd1Bya2ZWZ1hYd3NxTmJGVUpDRU05VGVRTGJP
ZDh3K1NVT0sxbApjQnA0WnhoYXRqREJucUwvbWpGa0pNbVR2RWtkemdZb3J3UE9KTkF5UEEwTGIv
c3VPNVFCVXJJL0JtZVozRFFECkNteG5JWkI0MTlrUnpmZzVnMG9Jamllamx5YWIvVGZEeVRoQnJi
ODFERVBUUG43Tlk4KzhVS09YV0d6enBvMDUKeVlRU0tPYnpaQ0ZCMHBXejRSMmJLZHVKWkxRM0J1
V2x2YnJTTllnZzF3SURBUUFCb0FBd0RRWUpLb1pJaHZjTgpBUUVMQlFBRGdnRUJBQkx2Z1hlTXVQ
TEJvQ0F5dnFCaU5LRjROcCtXa05zZlZCekluSUpQVDlmZW14OW9OcTlvCmxDV3djcFZxOFFKdGVJ
OGxyNCtycEpEQ1BaQ0s4S2kvVklBZDdpWTExaXRpYk1mWHZWaVhKQnNieDhiVHEzR3UKa21xMVFr
WVJuYnkzWm95cXYzeWFseHcvVWVwb2szM1lSMzhiLzBKanFtOHZ1V0h5aGRHWDM2Tk92MGF6NGFN
MwptUlBvcWlmNkM3cHFDTG1FU2lBeTNVSVRXd1ZEM3ZEVFczQzZwUEVzK0ZTOHk0YVZ4VGlqYWFW
cDNvakNxTlFGCm5wUzZzRDZNOCt5L0hFVmZHQlVqeEtsRndnWkExQjFEZzVtamcraUdUcTNQcFFD
RVppN0tHZU9vcklmTThuL0YKVXU1TmczQlNXS3hwMHlaMnR1TUVDK2FWVXRXemJ1Y3oyV2c9Ci0t
LS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo=
  usages:
  - digital signature
  - key encipherment
  - server auth
 ```


What is the Condition of the newly created Certificate Signing Request object?

```bash
kubectl get certificatesigningrequests.certificates.k8s.io akshay
NAME     AGE     REQUESTOR          CONDITION
akshay   2m48s   kubernetes-admin   Pending

```

Approve the CSR Request
```bash
master $ kubectl certificate approve akshay
certificatesigningrequest.certificates.k8s.io/akshay approved

```

- How many CSR requests are available on the cluster?
- Who requested csr-* requests

```bash
master $ kubectl get certificatesigningrequests.certificates.k8s.io  -o wide
NAME        AGE   REQUESTOR                 CONDITION
akshay      5m    kubernetes-admin          Approved,Issued
csr-2gth6   19m   system:bootstrap:96771a   Approved,Issued
csr-b94hf   19m   system:node:master        Approved,Issued
```

what is access requested with agent-smith
```bash
ubectl get csr agent-smith -o yaml
apiVersion: certificates.k8s.io/v1beta1
kind: CertificateSigningRequest
metadata:
  creationTimestamp: "2020-05-14T09:07:01Z"
  name: agent-smith
  resourceVersion: "2180"
  selfLink: /apis/certificates.k8s.io/v1beta1/certificatesigningrequests/agent-smith
  uid: 6f4cdead-a0c7-4095-aec4-aac00dab4caa
spec:
  groups:
  - system:masters
  - system:authenticated
  request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1dEQ0NBVUFDQVFBd0V6RVJNQThHQTFVRUF3d0libVYzTFhWelpYSXdnZ0VpTUEwR0NTcUdTSWIzRFFFQgpBUVVBQTRJQkR3QXdnZ0VLQW9JQkFRRE8wV0pXK0RYc0FKU0lyanBObzV2UklCcGxuemcrNnhjOStVVndrS2kwCkxmQzI3dCsxZUVuT041TXVxOTlOZXZtTUVPbnJEVU8vdGh5VnFQMncyWE5JRFJYall5RjQwRmJtRCs1eld5Q0sKeTNCaWhoQjkzTUo3T3FsM1VUdlo4VEVMcXlhRGtuUmwvanYvU3hnWGtvazBBQlVUcFdNeDRCcFNpS2IwVSt0RQpJRjVueEF0dE1Wa0RQUTdOYmVaUkc0M2IrUVdsVkdSL3o2RFdPZkpuYmZlek90YUF5ZEdMVFpGQy93VHB6NTJrCkVjQ1hBd3FDaGpCTGt6MkJIUFI0Sjg5RDZYYjhrMzlwdTZqcHluZ1Y2dVAwdEliT3pwcU52MFkwcWRFWnB3bXcKajJxRUwraFpFV2trRno4MGxOTnR5VDVMeE1xRU5EQ25JZ3dDNEdaaVJHYnJBZ01CQUFHZ0FEQU5CZ2txaGtpRwo5dzBCQVFzRkFBT0NBUUVBUzlpUzZDMXV4VHVmNUJCWVNVN1FGUUhVemFsTnhBZFlzYU9SUlFOd0had0hxR2k0CmhPSzRhMnp5TnlpNDRPT2lqeWFENnRVVzhEU3hrcjhCTEs4S2czc3JSRXRKcWw1ckxaeTlMUlZyc0pnaEQ0Z1kKUDlOTCthRFJTeFJPVlNxQmFCMm5XZVlwTTVjSjVURjUzbGVzTlNOTUxRMisrUk1uakRRSjdqdVBFaWM4L2RoawpXcjJFVU02VWF3enlrcmRISW13VHYybWxNWTBSK0ROdFYxWWllKzBIOS9ZRWx0K0ZTR2poNUw1WVV2STFEcWl5CjRsM0UveTNxTDcxV2ZBY3VIM09zVnBVVW5RSVNNZFFzMHFXQ3NiRTU2Q0M1RGhQR1pJcFVibktVcEF3a2ErOEUKdndRMDdqRytocGtueG11RkFlWHhnVXdvZEFMYUo3anUvVERJY3c9PQotLS0tLUVORCBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0K
  usages:
  - digital signature
  - key encipherment
  - server auth
  username: agent-x

# Deny the request
master $ kubectl certificate deny agent-smith
certificatesigningrequest.certificates.k8s.io/agent-smith approved

```
