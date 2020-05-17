# Certificate API

what is the CA server... and where is it.
> the CA is just a key and certificate

The server that holds this.. is the CA server.
`kubeadm` stores key in the master,so master is the `CA server`

kube has a built in api for signing request.

1. Create CertificateSigningRequest Object
2. Review Request
3. Approve Request
4. Share the certificate


`kubectl get csr`

Remember, the CSR must be encoded 

`kubectl certificate approve jane`
