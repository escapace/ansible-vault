```sh
  if ! auth_method_exits "jwt"; then
    vault auth enable jwt

    vault write auth/jwt/role/nomad-workloads - <<EOF
{
  "role_type": "jwt",
  "bound_audiences": "vault.service.consul",
  "user_claim": "/nomad_job_id",
  "user_claim_json_pointer": true,
  "claim_mappings": {
    "nomad_namespace": "nomad_namespace",
    "nomad_job_id": "nomad_job_id"
  },
  "token_period": "30m",
  "token_type": "service",
  "token_policies": ["nomad-workloads"]
}
EOF

  vault write auth/jwt/config jwks_ca_pem="@${nomad_ca_file_path}" - <<EOF
{
  "jwks_url": "https://nomad.service.consul:4646/.well-known/jwks.json",
  "jwt_supported_algs": ["RS256"],
  "default_role": "nomad-workloads"
}
EOF
  fi
```
