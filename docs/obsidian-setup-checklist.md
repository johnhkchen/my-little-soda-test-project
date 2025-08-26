# Obsidian Self-Hosted Setup Checklist

## Overview
Setting up Obsidian with free, self-hosted, instant sync using CouchDB and LiveSync plugin.

## System Architecture
- **Obsidian App**: Desktop/mobile clients with local note storage
- **CouchDB**: Self-hosted database for note synchronization
- **LiveSync Plugin**: Handles encrypted sync between devices and CouchDB
- **Reverse Proxy**: HTTPS termination (required for mobile devices)

## Infrastructure Setup

### 1. CouchDB Docker Container
- [ ] Create docker-compose configuration for CouchDB 3.3.3
- [ ] Configure environment variables:
  - [ ] PUID/PGID for proper permissions
  - [ ] TZ (timezone)
  - [ ] COUCHDB_USER (change from default)
  - [ ] COUCHDB_PASSWORD (secure password)
- [ ] Map persistent volumes:
  - [ ] `/opt/couchdb/data` for database files
  - [ ] `/opt/couchdb/etc/local.d` for configuration
- [ ] Expose port 5984
- [ ] Set restart policy to `unless-stopped`
- [ ] Deploy and verify container is running

### 2. CouchDB Initial Configuration
- [ ] Access CouchDB admin interface at `http://server-ip:5984/_utils`
- [ ] Login with credentials from docker-compose
- [ ] Configure as Single Node:
  - [ ] Navigate to Setup → Configure as Single Node
  - [ ] Enter admin credentials
  - [ ] Complete node configuration
- [ ] Verify installation:
  - [ ] Navigate to Verify → Verify Installation
  - [ ] Confirm all 6 checks pass with "Success!" message

### 3. CouchDB Database Setup
- [ ] Create database:
  - [ ] Navigate to Databases → Create Database
  - [ ] Name: `obsidiandb` (or user-specific like `obsidiandb_username`)
  - [ ] Select "Non-partitioned" option
- [ ] Configure database settings (9 required configurations):
  - [ ] `chttpd` → `require_valid_user` → `true`
  - [ ] `chttpd_auth` → `require_valid_user` → `true`
  - [ ] `httpd` → `WWW-Authenticate` → `Basic realm="couchdb"`
  - [ ] `httpd` → `enable_cors` → `true`
  - [ ] `chttpd` → `enable_cors` → `true`
  - [ ] `chttpd` → `max_http_request_size` → `4294967296`
  - [ ] `couchdb` → `max_document_size` → `50000000`
  - [ ] `cors` → `credentials` → `true`
  - [ ] `cors` → `origins` → `app://obsidian.md,capacitor://localhost,http://localhost`

## Client Setup

### 4. Obsidian Desktop Client
- [ ] Download and install Obsidian desktop application
- [ ] Create new vault:
  - [ ] Choose vault name
  - [ ] Select vault location (e.g., Documents/Obsidian)
  - [ ] Create vault
- [ ] Install LiveSync plugin:
  - [ ] Settings → Community plugins → Turn on community plugins
  - [ ] Browse → Search "Self-hosted LiveSync" by voratamoroz
  - [ ] Install and enable plugin

### 5. LiveSync Plugin Configuration
- [ ] Open plugin settings (🛰️ satellite icon)
- [ ] Configure remote connection:
  - [ ] Remote Type: CouchDB
  - [ ] URI: `http://server-ip:5984` (or HTTPS if using reverse proxy)
  - [ ] Username: CouchDB username
  - [ ] Password: CouchDB password
  - [ ] Database name: `obsidiandb`
- [ ] Test connection (should show "Connected successfully")
- [ ] Check database configuration (all items should have purple checkmarks)
- [ ] Apply settings
- [ ] Configure encryption (recommended):
  - [ ] Enable End-to-end encryption
  - [ ] Set secure passphrase
  - [ ] Apply encryption settings
- [ ] Set sync mode to "LiveSync"
- [ ] Verify sync status shows "Sync: zZz" (standby mode)

### 6. Mobile Device Setup
- [ ] Install Obsidian mobile app (iOS/Android)
- [ ] Repeat LiveSync plugin configuration for each device
- [ ] Ensure HTTPS access is available (required for mobile)
- [ ] Test sync between devices

## Security & Access

### 7. Reverse Proxy Configuration
- [ ] Set up reverse proxy (Nginx Proxy Manager recommended)
- [ ] Configure SSL/TLS certificate
- [ ] Set up domain/subdomain for CouchDB access
- [ ] Update LiveSync plugin URI to use HTTPS endpoint
- [ ] Test HTTPS connectivity

### 8. Network Security
- [ ] Configure firewall rules for CouchDB port
- [ ] Consider VPN access for additional security
- [ ] Regular backup strategy for CouchDB data
- [ ] Monitor CouchDB logs for unusual activity

## Testing & Validation

### 9. Sync Verification
- [ ] Create test note on one device
- [ ] Verify note appears on other devices
- [ ] Test offline functionality
- [ ] Verify encryption/decryption works across devices
- [ ] Test conflict resolution

### 10. Performance & Monitoring
- [ ] Monitor CouchDB resource usage
- [ ] Set up log rotation for CouchDB
- [ ] Document backup and recovery procedures
- [ ] Create maintenance schedule

## Multi-User Setup (Optional)
- [ ] Create separate databases for each user
- [ ] Configure user-specific access controls
- [ ] Document user onboarding process

## Documentation
- [ ] Document server configuration
- [ ] Create user guide for new devices
- [ ] Document troubleshooting procedures
- [ ] Create backup/restore procedures

## Notes
- Each device maintains a local copy for offline access
- Encryption passphrase must be identical across all devices
- Mobile devices require HTTPS for sync functionality
- Consider using Cloudflare Tunnels with Nginx Proxy Manager for external access