# Ansible Role: Ledger Download

Downloads Radix ledger data using either AWS S3 sync or community snapshots from [snapshots.radix.live](https://snapshots.radix.live).

This role does **not** manage the Radix node service. Stop the service before running and start it after in your playbook.

## Role Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `ledger_download_method` | `snapshot` | Download method: `s3` or `snapshot` |
| `ledger_data_dir` | `/data` | Target directory for ledger data |
| `ledger_data_owner` | `radixdlt` | Owner user for data directory |
| `ledger_data_group` | `radixdlt` | Owner group for data directory |
| `ledger_s3_bucket` | `s3://babylon-mainnet-ledger-backups/...` | S3 source path (for `s3` method) |
| `ledger_s3_region` | `eu-west-3` | AWS region (for `s3` method) |
| `ledger_snapshot_url` | `https://snapshots.radix.live/latest-snapshot-INDEX.sh` | Snapshot script URL (for `snapshot` method) |
| `ledger_async_timeout` | `21600` | Async timeout in seconds (6 hours) |
| `ledger_async_poll` | `60` | Poll interval in seconds |

### AWS S3 method

AWS credentials are inherited from the target host's environment (`~/.aws/credentials` or environment variables). No credentials are passed through Ansible variables.

## Example Playbook

```yaml
- hosts: radix_nodes
  become: true
  tasks:
    - name: Stop radix node
      ansible.builtin.service:
        name: radixdlt-node
        state: stopped

    - name: Download ledger
      ansible.builtin.include_role:
        name: ledger-download
      vars:
        ledger_download_method: s3

    - name: Start radix node
      ansible.builtin.service:
        name: radixdlt-node
        state: started
```

## License

MIT
