# Prymna

Helm chart that deploys the platform runtime for
[katastroma](https://github.com/katastroma). Provisions
[pedalion](https://github.com/katastroma/pedalion) (the Application operator)
and the [zeugma](https://github.com/katastroma/zeugma) service (adapter service
for resolution and provisioning backend), along with their RBAC.

The adapter image is configurable — swap the image to swap the backend.
