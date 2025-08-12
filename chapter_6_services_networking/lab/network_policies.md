# Network Policies Lab Exercise

## Lab Task: Create Internal Application Egress Policy

Create a network policy to allow egress traffic from the Internal application only to the payroll-service and db-service.

### Requirements

- Use the spec given below
- You might want to enable ingress traffic to the pod to test your rules in the UI
- Ensure that you allow egress traffic to DNS ports TCP and UDP (port 53) to enable DNS resolution from the internal pod

### Policy Specifications

| Specification | Value |
|---------------|-------|
| **Policy Name** | `internal-policy` |
| **Policy Type** | Egress |
| **Egress Allow** | payroll |
| **Payroll Port** | 8080 |
| **Egress Allow** | mysql |
| **MySQL Port** | 3306 |

---

## Solution: Network Policy Configuration

### Complete Network Policy with DNS Resolution

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: internal-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      name: internal
  policyTypes:
    - Egress
  egress:
    # Allow traffic to payroll service
    - to:
       - podSelector:
           matchLabels:
             name: payroll
      ports:
        - protocol: TCP
          port: 8080
    # Allow traffic to mysql service
    - to:
       - podSelector:
           matchLabels:
             name: mysql
      ports:
        - protocol: TCP
          port: 3306
    # Allow DNS resolution (TCP and UDP on port 53)
    - to: []
      ports:
        - protocol: TCP
          port: 53
        - protocol: UDP
          port: 53
```

### Key Configuration Points

- **Target Pods**: Selects pods with label `name: internal`
- **Policy Type**: Egress only (controls outgoing traffic)
- **Allowed Destinations**:
  - Payroll service on port 8080
  - MySQL service on port 3306
  - DNS servers on port 53 (TCP and UDP)

---

## Apply the Network Policy

```sh
# Create the network policy
kubectl apply -f internal-policy.yaml

# Verify the policy was created
kubectl get networkpolicies

# Describe the policy details
kubectl describe networkpolicy internal-policy
```

---

## Testing the Network Policy

### Verification Commands

```sh
# Check if internal pod exists
kubectl get pods -l name=internal

# Test connectivity to allowed services
kubectl exec -it <internal-pod-name> -- curl http://payroll-service:8080
kubectl exec -it <internal-pod-name> -- nc -zv mysql-service 3306

# Test DNS resolution (should work)
kubectl exec -it <internal-pod-name> -- nslookup payroll-service

# Test connectivity to blocked services (should fail)
kubectl exec -it <internal-pod-name> -- curl http://other-service:80
```

### Expected Results

✅ **Should Work:**
- Connections to payroll service on port 8080
- Connections to mysql service on port 3306
- DNS resolution (nslookup, dig commands)

❌ **Should Fail:**
- Connections to any other services
- Connections to payroll/mysql on different ports
- Direct internet access (unless explicitly allowed)

---

## Optional: Add Ingress Rules for Testing

If you need to test the internal application via UI, add ingress rules:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: internal-policy-with-ingress
  namespace: default
spec:
  podSelector:
    matchLabels:
      name: internal
  policyTypes:
    - Egress
    - Ingress
  # Ingress rules for testing
  ingress:
    - from: []
      ports:
        - protocol: TCP
          port: 8080
  # Egress rules (same as above)
  egress:
    - to:
       - podSelector:
           matchLabels:
             name: payroll
      ports:
        - protocol: TCP
          port: 8080
    - to:
       - podSelector:
           matchLabels:
             name: mysql
      ports:
        - protocol: TCP
          port: 3306
    - to: []
      ports:
        - protocol: TCP
          port: 53
        - protocol: UDP
          port: 53
```

---

## Troubleshooting

### Common Issues

1. **DNS Resolution Fails**
   - Ensure DNS egress rules are included
   - Check if kube-dns/coredns pods are accessible

2. **Policy Not Working**
   - Verify CNI supports NetworkPolicies
   - Check pod labels match exactly
   - Confirm policy is in correct namespace

3. **Connection Timeouts**
   - Verify target services are running
   - Check service names and ports
   - Ensure network policy allows the traffic

### Debug Commands

```sh
# Check pod labels
kubectl get pods --show-labels

# Verify service endpoints
kubectl get endpoints payroll-service mysql-service

# Check if CNI supports network policies
kubectl get nodes -o yaml | grep -i network

# View network policy events
kubectl describe networkpolicy internal-policy
```

---

## Key Takeaways

1. **Egress Control**: Network policies can restrict outbound traffic from pods
2. **DNS Requirements**: Always include DNS egress rules for name resolution
3. **Label Matching**: Pod selectors must exactly match pod labels
4. **Testing**: Use kubectl exec to verify policy effectiveness
5. **CNI Dependency**: Ensure your cluster's CNI supports NetworkPolicies