NOTES:
** Please be patient while the chart is being deployed **

The LiteSpeed Kubernetes ADC controller has been installed.

Get the application URL by running these commands:

 NOTE: It may take a few minutes for the LoadBalancer IP to be available.
        You can watch its status by running 'kubectl get --namespace ls-k8s-webadc svc -w ls-k8s-webadc'

    export SERVICE_IP=$(kubectl get svc --namespace ls-k8s-webadc ls-k8s-webadc -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
    echo "Visit http://${SERVICE_IP} to access your application via HTTP."
    echo "Visit https://${SERVICE_IP} to access your application via HTTPS."
    echo "Visit https://${SERVICE_IP}:<nil> to access the load balancer's configuration."

An example Ingress that makes use of the controller:

  apiVersion: networking.k8s.io/v1
  kind: Ingress
  metadata:
    annotations:
      kubernetes.io/ingress.class: lslbd
    name: example
    namespace: foo
  spec:
    rules:
      - host: www.example.com
        http:
          paths:
            - backend:
                service:
                  name: exampleService
                  port:
                    number: 80
              path: /
              pathType: Prefix
    # This section is only required if TLS is to be enabled for the Ingress
    tls:
        - hosts:
            - www.example.com
          secretName: example-tls

If TLS is enabled for the Ingress, a Secret containing the certificate and key must also be provided:

  apiVersion: v1
  kind: Secret
  metadata:
    name: example-tls
    namespace: foo
  data:
    tls.crt: <base64 encoded cert>
    tls.key: <base64 encoded key>
  type: kubernetes.io/tls