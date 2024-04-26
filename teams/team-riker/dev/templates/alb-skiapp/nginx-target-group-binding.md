apiVersion: elbv2.k8s.aws/v1beta1
kind: TargetGroupBinding
metadata:
  name: nginx-ingress-tgb
  namespace: ingress-nginx
spec:
  serviceRef: 
    name: ingress-nginx-controller
    port: 80
  targetGroupARN: "{{ .Values.spec.target_group_arn }}"
