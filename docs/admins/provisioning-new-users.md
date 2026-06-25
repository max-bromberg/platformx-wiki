# New user provisioning

When provisioning a new user, set their ZFS dataset quota on `sprlab005`. The loop below applies the standard 50 GB per-user quota to every home account (UID 1000 and above) on the `spr-storage/home` dataset:

```bash
for uid in $(getent passwd | awk -F: '$6 ~ /^\/home\// && $3>=1000 && $3<65534 {print $3}'); do
  sudo zfs set userquota@${uid}=50G spr-storage/home
done
```

Run this from the admin account with `sudo`. To raise a single user's quota, set `userquota@<uid>` to the agreed value rather than the 50 GB default.
