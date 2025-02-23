---
subcategory: "Container Registry Integrations"
layout: "lacework"
page_title: "Lacework: lacework_integration_gar"
description: |-
  Create and manage Google Artifact Registry (GAR) integrations
---

# lacework\_integration\_gar

Use this resource to integrate a Google Artifact Registry (GAR) with Lacework to assess, identify,
and report vulnerabilities found in the operating system software packages in a Docker container
image.

## Example Usage

```hcl
resource "lacework_integration_gar" "example" {
  name            = "GAR Example"
  registry_domain = "us-west1-docker.pkg.dev"
  credentials {
    client_id      = "123456789012345678900"
    client_email   = "email@abc-project-name.iam.gserviceaccount.com"
    private_key_id = "1234abcd1234abcd1234abcd1234abcd1234abcd"
    private_key    = "-----BEGIN PRIVATE KEY-----\n ... -----END PRIVATE KEY-----\n"
  }
}
```

## Example Loading Credentials from Local File

Alternatively, this example shows how to load a [service account key created](https://cloud.google.com/iam/docs/creating-managing-service-account-keys#creating_service_account_keys)
using the Cloud Console or the `gcloud` command-line tool located on a local file on disk:

```hcl
locals {
  gar_credentials = jsondecode(file("/path/to/creds.json"))
}

resource "lacework_integration_gar" "example" {
  name            = "GAR Example"
  registry_domain = "us-west1-docker.pkg.dev"
  credentials {
    client_id      = local.gar.client_id
    client_email   = local.gar.client_email
    private_key_id = local.gar.private_key_id
    private_key    = local.gar.private_key
  }
}
```

## Example Using Limits

```hcl
resource "lacework_integration_gar" "example" {
  name            = "GAR Example"
  registry_domain = "us-west1-docker.pkg.dev"
  credentials {
    client_id      = "123456789012345678900"
    client_email   = "email@abc-project-name.iam.gserviceaccount.com"
    private_key_id = "1234abcd1234abcd1234abcd1234abcd1234abcd"
    private_key    = "-----BEGIN PRIVATE KEY-----\n ... -----END PRIVATE KEY-----\n"
  }

  limit_num_imgs        = 10
  limit_by_tags         = ["dev*", "*test"]
  limit_by_repositories = ["repo/my-image", "repo/other-image"]

  limit_by_label {
    key   = "key"
    value = "value"
  }

  limit_by_label {
    key   = "key"
    value = "value2"
  }

  limit_by_label {
    key   = "foo"
    value = "bar"
  }
}
```

## Argument Reference

The following arguments are supported:

* `name` - (Required) The integration name.
* `registry_domain` - (Required) The GAR domain, which specifies the location where you store the images. For a list of supported domains, see [Supported Registry Domains](#supported-registry-domains) below.
* `credentials` - (Required) The credentials needed by the integration. See [Credentials](#credentials) below for details.
* `enabled` - (Optional) The state of the external integration. Defaults to `true`.
* `limit_num_imgs` - (Optional) The maximum number of newest container images to assess per repository. Must be one of `5`, `10`, or `15`. Defaults to `5`.
* `limit_by_tags` - (Optional) A list of image tags to limit the assessment of images with matching tags. If you specify `limit_by_tags` and `limit_by_label` limits, they function as an `AND`.
* `limit_by_label` - (Optional) A list of key/value labels to limit the assessment of images. If you specify `limit_by_tags` and `limit_by_label` limits, they function as an `AND`.
* `limit_by_repositories` - (Optional) A list of repositories to assess.

The `limit_by_label` block can be defined multiple times to define multiple label limits, it supports:
* `key` - (Required) The key of the label.
* `value` - (Required) The value of the label.

### Credentials

`credentials` supports the following arguments:

* `client_id` - (Required) The service account client ID.
* `client_email` - (Required) The service account client email.
* `private_key_id` - (Required) The service account private key ID.
* `private_key` - (Required) The service account private key.

~> **Note:** The service account used for this integration requires the `storage.objectViewer` role for access to the Google project that contains the Google Artifact Registry (GAR). The role can be granted at the project level or the bucket level. If granting the role at the bucket level, you must grant the role to the default bucket called `artifacts.[YourProjectID].appspot.com`. In addition, the client must have access to the Google Artifact Registry API, Cloud Resource Manager API, and billing must be enabled.

### Supported Registry Domains

The list of supported GAR domains is:
* `northamerica-northeast1-docker.pkg.dev`
* `us-central1-docker.pkg.dev`
* `us-east1-docker.pkg.dev`
* `us-east4-docker.pkg.dev`
* `us-west1-docker.pkg.dev`
* `us-west2-docker.pkg.dev`
* `us-west3-docker.pkg.dev`
* `us-west4-docker.pkg.dev`
* `southamerica-east1-docker.pkg.dev`
* `europe-north1-docker.pkg.dev`
* `europe-west1-docker.pkg.dev`
* `europe-west2-docker.pkg.dev`
* `europe-west3-docker.pkg.dev`
* `europe-west4-docker.pkg.dev`
* `europe-west6-docker.pkg.dev`
* `asia-east1-docker.pkg.dev`
* `asia-east2-docker.pkg.dev`
* `asia-northeast1-docker.pkg.dev`
* `asia-northeast2-docker.pkg.dev`
* `asia-northeast3-docker.pkg.dev`
* `asia-south1-docker.pkg.dev`
* `asia-southeast1-docker.pkg.dev`
* `asia-southeast2-docker.pkg.dev`
* `australia-southeast1-docker.pkg.dev`
* `asia-docker.pkg.dev`
* `europe-docker.pkg.dev`
* `us-docker.pkg.dev`

## Import

A Lacework GAR integration can be imported using a `INT_GUID`, e.g.

```
$ terraform import lacework_integration_gar.example EXAMPLE_1234BAE1E42182964D23973F44CFEA3C4AB63B99E9A1EC5
```
-> **Note:** To retreive the `INT_GUID` from existing integrations in your account, use the
	Lacework CLI command `lacework integration list`. To install this tool follow
	[this documentation](https://github.com/lacework/go-sdk/wiki/CLI-Documentation#installation).

