This repo contains AWX/Ansible playbooks and template for a 3-node workflow:

1. **Collect VA Evidence** → `collect_va.yml` (runs on target inventory hosts)
2. **Merge VA Result** → `merge_va.yml` (runs on localhost/controller)
3. **Render MD** → `render_md.yml` (runs on localhost/controller)

**Critical:** Node labels in the workflow **must exactly** match:
- `Collect VA Evidence`
- `Merge VA Result`
- `Render MD`

Artifacts shared via `set_stats`:
- Collect → `va_json_files`
- Merge → `merged_json_file`
- Render → `va_report_md`

Place `report.md.j2` in the same project path with playbooks.
