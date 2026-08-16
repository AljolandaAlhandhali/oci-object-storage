# OCI Object Storage

A collection of Java applications that integrate with [Oracle Cloud Infrastructure (OCI) Object Storage](https://www.oracle.com/cloud/storage/object-storage/) to list, upload, and download objects in the `litedesk-oci-bucket` bucket.

## Repository Layout

This repository contains two independent Maven projects:

| Directory | Description |
|---|---|
| [`oci-object-storage-single-file-app/`](oci-object-storage-single-file-app/) | Lightweight Java 17 application. The entire implementation lives in one class and runs in two modes: a plain HTTP server for Postman testing and a command-line mode. |
| [`oci-object-storage-springboot/`](oci-object-storage-springboot/) | Production-oriented Spring Boot 4 REST application that exposes versioned JSON endpoints for listing, uploading, and downloading objects. |

Both projects share the same foundation:

- **Java 17** and **Apache Maven** (with Maven Wrapper)
- **OCI Java SDK** for Object Storage
- **API-key authentication** via the OCI config file at `~/.oci/config` (the Spring Boot project also supports instance principals on OCI Compute)
- Automatic Object Storage **namespace discovery**
- Target bucket: `litedesk-oci-bucket`

## Prerequisites

- JDK 17 or later
- Maven 3.9+ (or use each project's Maven Wrapper)
- An OCI tenancy with a user, API signing key, and IAM permissions
- Access to the `litedesk-oci-bucket` bucket in the region configured in your OCI profile

### OCI Configuration

Create the OCI SDK config file at `~/.oci/config` with a `DEFAULT` profile:

```ini
[DEFAULT]
user=<user-ocid>
fingerprint=<api-key-fingerprint>
tenancy=<tenancy-ocid>
region=<your-oci-region>
key_file=<path-to-private-pem>
```

Never commit the config file or private PEM key to source control. See the individual project READMEs for detailed setup, IAM policies, and troubleshooting.

## Building and Running

Build either project with its Maven Wrapper:

```powershell
cd oci-object-storage-springboot
.\mvnw.cmd clean package
```

Run the Spring Boot REST API:

```powershell
java -jar target\oci-object-storage-app.jar
```

The API is served at `http://localhost:8081`:

- `GET /api/v1/objects` — list objects
- `POST /api/v1/objects` — upload a file (multipart)
- `GET /api/v1/objects/download?objectName=<name>` — download an object

For the single-file application, see its [README](oci-object-storage-single-file-app/README.md) for HTTP server and command-line usage.

## Documentation

Each sub-project has its own detailed README:

- [Single-file app](oci-object-storage-single-file-app/README.md)
- [Spring Boot app](oci-object-storage-springboot/README.md)

## Security Notes

- Do not commit OCI config files, PEM keys, OCIDs, or credentials.
- Prefer instance principals over API keys when running on OCI Compute.
- Grant least-privilege IAM policies scoped to the required bucket and operations.
