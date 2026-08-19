## Copilot instructions for NetApp Data Migrator documentation

### Repository overview
Product: NetApp Data Migrator

NetApp Data Migrator is an enterprise-grade, multicloud data migration application that moves unstructured file data from on-premises or third-party NAS storage systems to NetApp cloud storage services. It runs on user-managed virtual machines and supports NFS and SMB file transfer protocols.

### Repository structure
- `_include/` – Shared content fragments included in multiple pages
- `media/` – Images used throughout the documentation

### Product-specific context

**Architecture and components:**
- *Control plane*: A Linux VM that acts as the central management layer. It manages projects, users, jobs, workers, and file servers; schedules and dispatches job runs; monitors job execution; enforces RBAC; and handles notifications.
- *Workers*: Linux or Windows VMs that perform the actual data operations (scanning, copying, syncing). A *Linux worker* supports NFS migrations; a *Windows worker* supports SMB migrations. Multiple workers can be deployed for scale. Workers are associated with one or more file servers and report statistics and system metrics to the control plane.
- *UI*: A web-based interface accessed via the control plane's private IP address over HTTPS.
- The control plane and workers are deployed from images downloaded from the NetApp Support Site and can be deployed on AWS, Azure, Google Cloud, or via OVA templates.

**Key concepts:**
- *Project*: Top-level organizational unit grouping all file servers, jobs, workers, and users related to a specific migration effort. Users are assigned roles at the project level.
- *Job*: A reusable, configurable construct that defines a migration task. Three job types exist: *Discovery*, *Migrate*, and *Cutover*.
- *Job run*: A single execution instance of a job, with its own status, logs, and metrics.
- *Export path*: A protocol-specific location (NFS export or SMB share) representing the data unit for Discovery, Migrate, or Cutover operations. Export paths can be auto-discovered or manually uploaded via CSV.
- *Discovery job*: Scans and inventories data on a source or destination file server; generates reports and histograms.
- *Migrate job*: Transfers data from source to destination; supports baseline and incremental sync.
- *Cutover job*: Final migration step; stops ongoing migrate jobs, performs a final sync, generates a Chain of Custody (CoC) report, and requires user approval.
- *Chain of Custody (CoC) report*: A checksum-based report generated during cutover to verify data integrity.
- *Bulk Migrate / Bulk Discover / Bulk Cutover*: Features that allow multiple export path mappings to be processed together in a single workflow.

**Naming conventions and terminology:**
- The product name is *NetApp Data Migrator* (not "Data Migrator" alone in formal references).
- User roles are *App Admin*, *Project Admin*, and *Project Viewer* (capitalized as shown).
- Job types are capitalized: *Discovery*, *Migrate*, *Cutover*.
- Job run states: *Ready*, *Running*, *Paused*, *Stopped*, *Errored*, *Failed*, *Blocked*, *Rejected*, *Approved*, *Completed*.
- File server status types: *Active*, *In Progress*, *Draft*, *Errored*.
- Export path status types after manual upload: *Valid*, *Invalid*, *Disabled*.
- *RBAC* stands for Role-Based Access Control.
- *CoC* stands for Chain of Custody.
- *GID/UID* mapping and *SID* mapping are used to preserve file ownership during NFS and SMB migrations respectively.
- Supported source file servers include any NAS (Dell Isilon, ONTAP, Vanilla Linux, Windows, Cloud Volumes ONTAP).
- Supported destination file servers: *Azure NetApp Files (ANF)*, *Google Cloud NetApp Volumes (GCNV)*, *Amazon FSx for NetApp ONTAP (FSxN)*, *Cloud Volumes ONTAP*.
- *SmartConnect Service IP (SSIP)* is a Dell Isilon–specific load-balancing feature.
- *GCNV Flex* service requires manual export path upload because auto-discovery is not supported.

### Typical user workflows

**Initial setup:** Register on NetApp Support Site → Download control plane and worker images → Deploy control plane and Linux worker VMs (AWS, Azure, Google Cloud, or OVA) → Create control plane VM and worker VMs → Optionally validate control plane VM → Access UI → Log in and reset credentials → Create first project

**Add file servers and workers:** Log in → Select project → Add file server (Other NAS or Dell Isilon) → Configure credentials and protocol version → Associate workers → Run pre-check → Finish

**Run a Discovery job:** Select file server → Configure Bulk Discover → Select export paths → Submit job → Monitor job run → Review discovery report and histogram

**Run a Migration job:** Select file server → Configure Bulk Migrate → Add source-to-destination mappings → Set options (permissions, incremental sync, exclusions, GID/UID or SID mapping) → Review precheck → Submit job → Monitor job runs → Retry errored files if needed

**Perform Cutover:** Ensure Migration jobs are complete → Configure Bulk Cutover → Run cutover job → Review Chain of Custody report → Approve to finalize migration
