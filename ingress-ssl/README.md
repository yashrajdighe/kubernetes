# How to use SSL certificates in Kubernetes Ingress Resource
1. Deploy NGINX Ingress controller on the k8s cluster.
2. Create a sample deployment, service and ingress.
3. Map the ingress with some domain.
4. Check that it works on HTTP.
5. For the SSL Certificates, we can purchase the SSL certs from some providers or can use Let's Encrypt
6. If you have ssl certs, then move to step 12, else we will generate let's encrypt certs for sample here
7. Install Certmanager on your machine (ubuntu/debian), update DOMAIN in below commands with your domain 
    ```console
    sudo snap install core; sudo snap refresh core
    sudo apt remove certbot
    sudo snap install --classic certbot
    sudo ln -s /snap/bin/certbot /usr/bin/certbot
    sudo certbot certonly --manual --preferred-challenges dns -d "DOMAIN"
    ```
8. Create a TXT based record with the details provided in the above command
9. Then verify the TXT record with below command
    ```console
    nslookup -type=TXT _acme-challenge.DOMAIN
    ```
10. If you get the same key and value in the above command, press ENTER and continue
11. Certs are generated on /etc/letsencrypt/live/DOMAIN bydefault, if generated on different path, check the output of the above command.
12. Now create a sercet using the cert and privateKey using below command
    ```console
    kubectl create secret tls SAMPLE-APP-TLS \
        --key PRIVATEKEY.pem \
        --cert CERT.pem
    ```
13. Add the tls resource in the ingress manifest and update it with the name of secret and hosts in tls section
    ```console
    tls:
      - hosts:
        - DOMAIN
        secretName: SAMPLE-APP-TLS
    ```
14. Apply the ingress and verify that the domain is accessible on HTTPS (secured connection)

### Reference DOcs
1. https://devopscube.com/configure-ingress-tls-kubernetes/
2. https://www.digitalocean.com/community/tutorials/how-to-acquire-a-let-s-encrypt-certificate-using-dns-validation-with-acme-dns-certbot-on-ubuntu-18-04
3. https://ongkhaiwei.medium.com/generate-lets-encrypt-certificate-with-dns-challenge-and-namecheap-e5999a040708
4. https://www.digitalocean.com/community/tutorials/how-to-use-certbot-standalone-mode-to-retrieve-let-s-encrypt-ssl-certificates-on-ubuntu-20-04
