#### Google Cloud Computing Foundations | C02 Infrastructure in Google Cloud
#### M06: You Can’t Secure the Cloud, Right?

# Quiz: You Can’t Secure the Cloud, Right?

1. How are user identities created in Cloud IAM?
- [x] User identities are created outside of Google Cloud using a Google-administered domain
- [ ] User identities are created in the Cloud IAM section of the console
- [ ] User identities are created in the Cloud Identity Console in Google Cloud
- [ ] User Identities are inherited from Active Directory by default
> Creating users and groups within Google Cloud is not possible.

2. If a Cloud IAM policy gives you Owner permissions at the project level, your access to a resource in the project may be restricted by a more restrictive policy on that resource.
- [x] False
- [ ] True
> Policies are a union of the parent and the resource. If a parent policy is less restrictive, it overrides a more restrictive resource policy.

3. Scripted encryption keys is not an option with Google Cloud.
- [ ] Google encryption by default
- [ ] Scripted encryption keys (SEK)
- [ ] Customer-managed encryption keys (CMEK)
- [ ] Customer-supplied encryption Keys (CSEK)
> Scripted encryption keys is not an option with Google Cloud.