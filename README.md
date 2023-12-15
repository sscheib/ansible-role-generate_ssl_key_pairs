[![ansible-lint](https://github.com/sscheib/ansible-role-generate_ssl_key_pairs/actions/workflows/ansible-lint.yml/badge.svg)](https://github.com/sscheib/ansible-role-generate_ssl_key_pairs/actions/workflows/ansible-lint.yml) [![Publish latest release to Ansible Galaxy](https://github.com/sscheib/ansible-role-generate_ssl_key_pairs/actions/workflows/ansible-galaxy.yml/badge.svg?branch=main)](https://github.com/sscheib/ansible-role-generate_ssl_key_pairs/actions/workflows/ansible-galaxy.yml)

generate_ssl_key_pairs
=========

Generates SSL certificates on a PKI host and transfers the generated public and private key to the Ansible managed nodes. Optionally, the certificate authoritie's
certificate/s can be transferred as well as the optional Certificate Revocation List certificate/s.

Requirements
------------

This role requires a fully-setup Public Key Infrastructure (PKI).

Role Variables
--------------

# A note on templating connection variables

This role works with delegations (`delegate_to`). The SSL key pair generation runs on the host defined in `crt_pki_host`, so all tasks related to key generation will run on
`crt_pki_host`. Ansible, however, will try to lookup **templated connection variables** (such as `remote_user`) from the host we are delegating to (in this 
case `crt_pki_host`) since Ansible 2.9.10.
There is a [lengthy discussion on Ansible's GitHub ](https://github.com/ansible/ansible/issues/72776) that discusses this in more detail if you'd like to learn more about it.

What I'd like to point out is, if you make use of `host_vars` or `group_vars`, please make sure to define the connection variables (`crt_pki_host`, `crt_pki_host_remote_user`
and `crt_pki_host_remote_port`) in the `host_vars` of the host defined in `crt_pki_host` or the corresponding `group_vars`.
Of course, you can also define them in either `host_vars/all` or `group_vars/all` (as usual with Ansible).

Defining them in the `inventory_hostname` context will **not work**!

This does **not** affect users including the role with variables defined via `extra_vars`, on Play or on Task level, as these variables are valid in **every** host context.

## Mandatory variables

| variable                                     | default                               | required | description                                                           |
| :---------------------------------           | :-----------------------------------  | :------- | :---------------------------------------------------------------------|
| `crt_cert_fqdn`                              | unset                                 | true     | FQDN[^1] for the cert. This will be used to define the cert file name.|
| `crt_ca_priv_key_pass`                       | unset                                 | true     | Passphrase for the certificate authority (CA) private key             |
| `crt_pki_host`                               | unset                                 | true     | PKI host - this is where the key generation will happen               |
| `crt_pki_host_remote_user`                   | unset                                 | true     | Remote user to connect ot the PKI host to                             |

[^1]: Fully Qualified Domain Name

## Certificate suffixes

#### Variable to description
| variable                                                    | description                                                                                               |
| :---------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------- |
| `crt_priv_key_suffix`                                       | Private key suffix                                                                                        |
| `crt_cert_suffix`                                           | Certificate (aka public key) suffix                                                                       |
| `crt_csr_suffix`                                            | Certificate Signing Request (CSR) suffix                                                                  |
| `crt_crl_suffix`                                            | Certificate Revocation List (CRL) suffix                                                                  |

#### Variable to default variable
| variable                                                    | default variable                                                                                          |
| :---------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------- |
| `crt_priv_key_suffix`                                       | `_def_crt_priv_key_suffix`                                                                                |
| `crt_cert_suffix`                                           | `_def_crt_cert_suffix`                                                                                    |
| `crt_csr_suffix`                                            | `_def_crt_csr_suffix`                                                                                     |
| `crt_crl_suffix`                                            | `_def_crt_crl_suffix`                                                                                     |

#### Default variable to default value and requirement
| default variable                                            | default value                                                                                  | required |
| :---------------------------------------------------------- | :--------------------------------------------------------------------------------------------- | :------- |
| `_def_crt_priv_key_suffix`                                  | `key.pem`                                                                                      | false    |
| `_def_crt_cert_suffix`                                      | `cert.pem`                                                                                     | false    |
| `_def_crt_csr_suffix`                                       | `csr.pem`                                                                                      | false    |
| `_def_crt_crl_suffix`                                       | `crl.pem`                                                                                      | false    |

## Certificate Authority (CA)

#### Variable to description
| variable                                                    | description                                                                                               |
| :---------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------- |
| `crt_ca_root_dir`                                           | Root directory of the CA                                                                                  |
| `crt_ca_cert_name`                                          | Name of the CA certificate (aka public key)                                                               |
| `crt_ca_chain_cert_name`                                    | Name of the CA chain certificate (for intermediate CAs)                                                   |
| `crt_ca_priv_key_name`                                      | Name of the CA private key                                                                                |
| `crt_ca_priv_key_dir_path`                                  | Path of the CA's private keys directory                                                                   |
| `crt_ca_cert_dir_path`                                      | Path of the CA's certificates directory                                                                   |
| `crt_ca_csr_dir_path`                                       | Path of the CA's CSR directory                                                                            |
| `crt_ca_cert_path`                                          | Path of the CA certificate                                                                                |
| `crt_ca_chain_cert_path`                                    | Path of the CA chain certificate (for intermediate CAs)                                                   |
| `crt_ca_priv_key_path`                                      | Path of the CA private key                                                                                |
| `crt_ca_fetch_ca_cert`                                      | Whether to fetch the CA certificate                                                                       |
| `crt_ca_fetch_ca_chain_cert`                                | Whether to fetch the CA chain certificate (for intermediate CAs)                                          |

#### Variable to default variable
| variable                                                    | default variable                                                                                          |
| :---------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------- |
| `crt_ca_root_dir`                                           | `_def_crt_ca_root_dir`                                                                                    |
| `crt_ca_cert_name`                                          | `_def_crt_ca_cert_name`                                                                                   |
| `crt_ca_chain_cert_name`                                    | `_def_crt_ca_chain_cert_name`                                                                             |
| `crt_ca_priv_key_name`                                      | `_def_crt_ca_priv_key_name`                                                                               |
| `crt_ca_priv_key_dir_path`                                  | `_def_crt_ca_priv_key_dir_path`                                                                           |
| `crt_ca_cert_dir_path`                                      | `_def_crt_ca_cert_dir_path`                                                                               |
| `crt_ca_csr_dir_path`                                       | `_def_crt_ca_csr_dir_path`                                                                                |
| `crt_ca_cert_path`                                          | `_def_crt_ca_cert_path`                                                                                   |
| `crt_ca_chain_cert_path`                                    | `_def_crt_ca_chain_cert_path`                                                                             |
| `crt_ca_priv_key_path`                                      | `_def_crt_ca_priv_key_path`                                                                               |
| `crt_ca_fetch_ca_cert`                                      | `_def_crt_ca_fetch_ca_cert`                                                                               |
| `crt_ca_fetch_ca_chain_cert`                                | `_def_crt_ca_fetch_ca_chain_cert`                                                                         |

#### Default variable to default value and requirement
| default variable                                            | default value                                                                                  | required |
| :---------------------------------------------------------- | :--------------------------------------------------------------------------------------------- | :------- |
| `_def_crt_ca_root_dir`                                      | `/root/ca`                                                                                     | false    |
| `_def_crt_ca_cert_name`                                     | `ca.{{ _def_crt_cert_suffix }}`                                                                | false    |
| `_def_crt_ca_chain_cert_name`                               | `ca-chain.{{ _def_crt_cert_suffix }}`                                                          | false    |
| `_def_crt_ca_priv_key_name`                                 | `ca.{{ _def_crt_priv_key_suffix }}`                                                            | false    |
| `_def_crt_ca_priv_key_dir_path`                             | `{{ _def_crt_ca_root_dir }}/private`                                                           | false    |
| `_def_crt_ca_cert_dir_path`                                 | `{{ _def_crt_ca_root_dir }}/certs`                                                             | false    |
| `_def_crt_ca_csr_dir_path`                                  | `{{ _def_crt_ca_root_dir }}/csr`                                                               | false    |
| `_def_crt_ca_cert_path`                                     | `{{ _def_crt_ca_cert_dir_path }}/{{ _def_crt_ca_cert_name }}`                                  | false    |
| `_def_crt_ca_chain_cert_path`                               | `{{ _def_crt_ca_cert_dir_path }}/{{ _def_crt_ca_chain_cert_name }}`                            | false    |
| `_def_crt_ca_priv_key_path`                                 | `{{ _def_crt_ca_priv_key_dir_path }}/{{ _def_crt_ca_priv_key_name }}`                          | false    |
| `_def_crt_ca_fetch_ca_cert`                                 | `true`                                                                                         | false    |
| `_def_crt_ca_fetch_ca_chain_cert`                           | `true`                                                                                         | false    |

## Certificate Signing Request (CSR)

#### Variable to description
| variable                                                    | description                                                                                               |
| :---------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------- |
| `crt_csr_email`                                             | CSR email                                                                                                 |
| `crt_csr_org`                                               | CSR organization name (O)                                                                                 |
| `crt_csr_org_unit`                                          | CSR organization unit name (OU)                                                                           |
| `crt_csr_country`                                           | CSR country name (C)                                                                                      |
| `crt_csr_state`                                             | CSR state name (S)                                                                                        |
| `crt_csr_loc`                                               | CSR locality name (L)                                                                                     |
| `crt_csr_key_usage`                                         | CSR key usage                                                                                             |
| `crt_csr_extended_key_usage`                                | CSR extended key usage                                                                                    |

#### Variable to default variable
| variable                                                    | default variable                                                                                          |
| :---------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------- |
| `crt_csr_email`                                             | `_def_crt_csr_email`                                                                                      |
| `crt_csr_org`                                               | `_def_crt_csr_org`                                                                                        |
| `crt_csr_org_unit`                                          | `_def_crt_csr_org_unit`                                                                                   |
| `crt_csr_country`                                           | `_def_crt_csr_country`                                                                                    |
| `crt_csr_state`                                             | `_def_crt_csr_state`                                                                                      |
| `crt_csr_loc`                                               | `_def_crt_csr_loc`                                                                                        |
| `crt_csr_key_usage`                                         | `_def_crt_csr_key_usage`                                                                                  |
| `crt_csr_extended_key_usage`                                | `_def_crt_csr_extended_key_usage`                                                                         |

#### Default variable to default value and requirement
| default variable                                            | default value                                                                                  | required |
| :---------------------------------------------------------- | :--------------------------------------------------------------------------------------------- | :------- |
| `_def_crt_csr_email`                                        | `owner@example.com`                                                                            | false    |
| `_def_crt_csr_org`                                          | `Default Organization`                                                                         | false    |
| `_def_crt_csr_org_unit`                                     | `Default Organizational Unit`                                                                  | false    |
| `_def_crt_csr_country`                                      | `XX`                                                                                           | false    |
| `_def_crt_csr_state`                                        | `Default Province`                                                                             | false    |
| `_def_crt_csr_loc`                                          | `Default Locality`                                                                             | false    |
| `_def_crt_csr_key_usage`                                    | `['digitalSignature', 'keyEncipherment', 'keyAgreement']`                                      | false    |
| `_def_crt_csr_extended_key_usage`                           | `['clientAuth', 'serverAuth']`                                                                 | false    |

## Certificate Revocation List (CRL)

#### Variable to description
| variable                                                    | description                                                                                               |
| :---------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------- |
| `crt_ca_fetch_ca_crl_cert`                                  | Whether to fetch the CRL certificate/s                                                                    |
| `crt_combine_ca_crl_certs`                                  | Whether to combine the CRLs into one CRL file (in case of multiple CRLs to fetch)                         |
| `crt_crl_dest_name`                                         | Destination name of the combined CRL files                                                                |
| `crt_ca_crl_certs`                                          | CA CRL certificates to download                                                                           |

#### Variable to default variable
| variable                                                    | default variable                                                                                          |
| :---------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------- |
| `crt_ca_fetch_ca_crl_cert`                                  | `_def_crt_ca_fetch_ca_crl_cert`                                                                           |
| `crt_combine_ca_crl_certs`                                  | `_def_crt_combine_ca_crl_certs`                                                                           |
| `crt_crl_dest_name`                                         | `_def_crt_crl_dest_name`                                                                                  |

#### Default variable to default value and requirement
| default variable                                            | default value                                                                                  | required |
| :---------------------------------------------------------- | :--------------------------------------------------------------------------------------------- | :------- |
| `_def_crt_ca_fetch_ca_crl_cert`                             | `true`                                                                                         | false    |
| `_def_crt_combine_ca_crl_certs`                             | `true`                                                                                         | false    |
| `_def_crt_crl_dest_name`                                    | `ca-chain.{{ _def_crt_crl_suffix }}`                                                           | false    |

## Private key

#### Variable to description
| variable                                                    | description                                                                                               |
| :---------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------- |
| `crt_priv_key_type`                                         | Private key type to generate                                                                              |
| `crt_priv_key_size`                                         | Private key length/size to generate                                                                       |
| `crt_force_priv_key_generation`                             | Whether to force private key regeneration (not idempotent!)                                               |

#### Variable to default variable
| variable                                                    | default variable                                                                                          |
| :---------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------- |
| `crt_priv_key_type`                                         | `_def_crt_priv_key_type`                                                                                  |
| `crt_priv_key_size`                                         | `_def_crt_priv_key_size`                                                                                  |
| `crt_force_priv_key_generation`                             | `_def_crt_force_priv_key_generation`                                                                      |

#### Default variable to default value and requirement
| default variable                                            | default value                                                                                  | required |
| :---------------------------------------------------------- | :--------------------------------------------------------------------------------------------- | :------- |
| `_def_crt_priv_key_type`                                    | `RSA`                                                                                          | false    |
| `_def_crt_priv_key_size`                                    | `4096`                                                                                         | false    |
| `_def_crt_force_priv_key_generation`                        | `false`                                                                                        | false    |

## General

#### Variable to description
| variable                                                    | description                                                                                               |
| :---------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------- |
| `crt_local_dest_path`                                       | Path on the Ansible controller where to (temporarily) store fetched certificates                          |
| `crt_local_dest_path_owner`                                 | Owner of `{{ crt_local_dest_path }}`                                                                      |
| `crt_local_dest_path_group`                                 | Group of `{{ crt_local_dest_path }}`                                                                      |
| `crt_local_dest_path_mode`                                  | Mode of `{{ crt_local_dest_path }}`                                                                       |
| `crt_quiet_assert`                                          | Whether to quiet assert                                                                                   |
| `crt_remove_temporary_local_certificates`                   | Whether to remove locally stored temporary certificates (will break idempotency when set to `true`)       |

#### Variable to default variable
| variable                                                    | default variable                                                                                          |
| :---------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------- |
| `crt_local_dest_path`                                       | `_def_crt_local_dest_path`                                                                                |
| `crt_local_dest_path_owner`                                 | `_def_crt_local_dest_path_owner`                                                                          |
| `crt_local_dest_path_group`                                 | `_def_crt_local_dest_path_group`                                                                          |
| `crt_local_dest_path_mode`                                  | `_def_crt_local_dest_path_mode`                                                                           |
| `crt_quiet_assert`                                          | `_def_crt_quiet_assert`                                                                                   |
| `crt_remove_temporary_local_certificates`                   | `_def_crt_remove_temporary_local_certificates`                                                            |

#### Default variable to default value and requirement
| default variable                                            | default value                                                                                  | required |
| :---------------------------------------------------------- | :--------------------------------------------------------------------------------------------- | :------- |
| `_def_crt_local_dest_path`                                  | `/tmp/fetched`                                                                                 | false    |
| `_def_crt_local_dest_path_owner`                            | `{{ ansible_user }}`                                                                           | false    |
| `_def_crt_local_dest_path_group`                            | `{{ ansible_user }}`                                                                           | false    |
| `_def_crt_local_dest_path_mode`                             | `0700`                                                                                         | false    |
| `_def_crt_quiet_assert`                                     | `true`                                                                                         | false    |
| `_def_crt_remove_temporary_local_certificates`              | `true`                                                                                         | false    |

## Private key infrastructure (PKI) host

#### Variable to description
| variable                                                    | description                                                                                               |
| :---------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------- |
| `crt_pki_host_remote_port`                                  | SSH port of the PKI host                                                                                  |

#### Variable to default variable
| variable                                                    | default variable                                                                                          |
| :---------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------- |
| `crt_pki_host_remote_port`                                  | `_def_crt_pki_host_remote_port`                                                                           |

#### Default variable to default value and requirement
| default variable                                            | default value                                                                                  | required |
| :---------------------------------------------------------- | :--------------------------------------------------------------------------------------------- | :------- |
| `_def_crt_pki_host_remote_port`                             | `22`                                                                                           | false    |

## Managed node certificate paths and permissions

#### Variable to description
| variable                                                    | description                                                                                               |
| :---------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------- |
| `crt_remote_private_key_path`                               | Where to copy the private key to on the managed node                                                      |
| `crt_remote_private_key_owner`                              | Owner of `crt_remote_private_key_path`                                                                    |
| `crt_remote_private_key_group`                              | Group of `crt_remote_private_key_path`                                                                    |
| `crt_remote_private_key_mode`                               | Mode of `crt_remote_private_key_path`                                                                     |
| `crt_remote_private_key_dir_owner`                          | Owner of parent directory of `crt_remote_private_key_path`                                                |
| `crt_remote_private_key_dir_group`                          | Group of parent directory of `crt_remote_private_key_path`                                                |
| `crt_remote_private_key_dir_mode`                           | Mode of parent directory of `crt_remote_private_key_path`                                                 |
| `crt_remote_public_key_path`                                | Where to copy the public key to on the managed node                                                       |
| `crt_remote_public_key_owner`                               | Owner of `crt_remote_public_key_path`                                                                     |
| `crt_remote_public_key_group`                               | Group of `crt_remote_public_key_path`                                                                     |
| `crt_remote_public_key_mode`                                | Mode of `crt_remote_public_key_path`                                                                      |
| `crt_remote_public_key_dir_owner`                           | Owner of parent directory of `crt_remote_public_key_path`                                                 |
| `crt_remote_public_key_dir_group`                           | Group of parent directory of `crt_remote_public_key_path`                                                 |
| `crt_remote_public_key_dir_mode`                            | Mode of parent directory of `crt_remote_public_key_path`                                                  |
| `crt_remote_ca_public_key_path`                             | Where to copy the CA public key to on the managed node                                                    |
| `crt_remote_ca_public_key_owner`                            | Owner of `crt_remote_ca_public_key_path`                                                                  |
| `crt_remote_ca_public_key_group`                            | Group of `crt_remote_ca_public_key_path`                                                                  |
| `crt_remote_ca_public_key_mode`                             | Mode of `crt_remote_ca_public_key_path`                                                                   |
| `crt_remote_ca_public_key_dir_owner`                        | Owner of parent directory of `crt_remote_ca_public_key_path`                                              |
| `crt_remote_ca_public_key_dir_group`                        | Group of parent directory of `crt_remote_ca_public_key_path`                                              |
| `crt_remote_ca_public_key_dir_mode`                         | Mode of parent directory of `crt_remote_ca_public_key_path`                                               |
| `crt_remote_ca_chain_cert_path`                             | Where to copy the CA chain cert to on the managed node                                                    |
| `crt_remote_ca_chain_cert_owner`                            | Owner of `crt_remote_ca_chain_cert_path`                                                                  |
| `crt_remote_ca_chain_cert_group`                            | Group of `crt_remote_ca_chain_cert_path`                                                                  |
| `crt_remote_ca_chain_cert_mode`                             | Mode of `crt_remote_ca_chain_cert_path`                                                                   |
| `crt_remote_ca_chain_cert_dir_owner`                        | Owner of parent directory of `crt_remote_ca_chain_cert_path`                                              |
| `crt_remote_ca_chain_cert_dir_group`                        | Group of parent directory of `crt_remote_ca_chain_cert_path`                                              |
| `crt_remote_ca_chain_cert_dir_mode`                         | Mode of parent directory of `crt_remote_ca_chain_cert_path`                                               |
| `crt_remote_ca_crl_path`                                    | Where to copy the combined CRL certificate to on the managed node                                         |
| `crt_remote_ca_crl_owner`                                   | Owner of `crt_remote_ca_crl_path`                                                                         |
| `crt_remote_ca_crl_group`                                   | Group of `crt_remote_ca_crl_path`                                                                         |
| `crt_remote_ca_crl_mode`                                    | Mode of `crt_remote_ca_crl_path`                                                                          |
| `crt_remote_ca_crl_dir_owner`                               | Owner of parent directory of `crt_remote_ca_crl_path`                                                     |
| `crt_remote_ca_crl_dir_group`                               | Group of parent directory of `crt_remote_ca_crl_path`                                                     |
| `crt_remote_ca_crl_dir_mode`                                | Mode of parent directory of `crt_remote_ca_crl_path`                                                      |

#### Variable to default variable
| variable                                                    | default variable                                                                                          |
| :---------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------- |
| `crt_remote_private_key_path`                               | `_def_crt_remote_private_key_path`                                                                        |
| `crt_remote_private_key_owner`                              | `_def_crt_remote_private_key_owner`                                                                       |
| `crt_remote_private_key_group`                              | `_def_crt_remote_private_key_group`                                                                       |
| `crt_remote_private_key_mode`                               | `_def_crt_remote_private_key_mode`                                                                        |
| `crt_remote_private_key_dir_owner`                          | `_def_crt_remote_private_key_dir_owner`                                                                   |
| `crt_remote_private_key_dir_group`                          | `_def_crt_remote_private_key_dir_group`                                                                   |
| `crt_remote_private_key_dir_mode`                           | `_def_crt_remote_private_key_dir_mode`                                                                    |
| `crt_remote_public_key_path`                                | `_def_crt_remote_public_key_path`                                                                         |
| `crt_remote_public_key_owner`                               | `_def_crt_remote_public_key_owner`                                                                        |
| `crt_remote_public_key_group`                               | `_def_crt_remote_public_key_group`                                                                        |
| `crt_remote_public_key_mode`                                | `_def_crt_remote_public_key_mode`                                                                         |
| `crt_remote_public_key_dir_owner`                           | `_def_crt_remote_public_key_dir_owner`                                                                    |
| `crt_remote_public_key_dir_group`                           | `_def_crt_remote_public_key_dir_group`                                                                    |
| `crt_remote_public_key_dir_mode`                            | `_def_crt_remote_public_key_dir_mode`                                                                     |
| `crt_remote_ca_public_key_path`                             | `_def_crt_remote_ca_public_key_path`                                                                      |
| `crt_remote_ca_public_key_owner`                            | `_def_crt_remote_ca_public_key_owner`                                                                     |
| `crt_remote_ca_public_key_group`                            | `_def_crt_remote_ca_public_key_group`                                                                     |
| `crt_remote_ca_public_key_mode`                             | `_def_crt_remote_ca_public_key_mode`                                                                      |
| `crt_remote_ca_public_key_dir_owner`                        | `_def_crt_remote_ca_public_key_dir_owner`                                                                 |
| `crt_remote_ca_public_key_dir_group`                        | `_def_crt_remote_ca_public_key_dir_group`                                                                 |
| `crt_remote_ca_public_key_dir_mode`                         | `_def_crt_remote_ca_public_key_dir_mode`                                                                  |
| `crt_remote_ca_chain_cert_path`                             | `_def_crt_remote_ca_chain_cert_path`                                                                      |
| `crt_remote_ca_chain_cert_owner`                            | `_def_crt_remote_ca_chain_cert_owner`                                                                     |
| `crt_remote_ca_chain_cert_group`                            | `_def_crt_remote_ca_chain_cert_group`                                                                     |
| `crt_remote_ca_chain_cert_mode`                             | `_def_crt_remote_ca_chain_cert_mode`                                                                      |
| `crt_remote_ca_chain_cert_dir_owner`                        | `_def_crt_remote_ca_chain_cert_dir_owner`                                                                 |
| `crt_remote_ca_chain_cert_dir_group`                        | `_def_crt_remote_ca_chain_cert_dir_group`                                                                 |
| `crt_remote_ca_chain_cert_dir_mode`                         | `_def_crt_remote_ca_chain_cert_dir_mode`                                                                  |
| `crt_remote_ca_crl_path`                                    | `_def_crt_remote_ca_crl_path`                                                                             |
| `crt_remote_ca_crl_owner`                                   | `_def_crt_remote_ca_crl_owner`                                                                            |
| `crt_remote_ca_crl_group`                                   | `_def_crt_remote_ca_crl_group`                                                                            |
| `crt_remote_ca_crl_mode`                                    | `_def_crt_remote_ca_crl_mode`                                                                             |
| `crt_remote_ca_crl_dir_owner`                               | `_def_crt_remote_ca_crl_dir_owner`                                                                        |
| `crt_remote_ca_crl_dir_group`                               | `_def_crt_remote_ca_crl_dir_group`                                                                        |
| `crt_remote_ca_crl_dir_mode`                                | `_def_crt_remote_ca_crl_dir_mode`                                                                         |

#### Default variable to default value and requirement
| default variable                                            | default value                                                                                  | required |
| :---------------------------------------------------------- | :--------------------------------------------------------------------------------------------- | :------- |
| `_def_crt_remote_private_key_path`                          | `/root/certs/{{ inventory_hostname }}.{{ _def_crt_priv_key_suffix }}`                          | false    |
| `_def_crt_remote_private_key_owner`                         | `root`                                                                                         | false    |
| `_def_crt_remote_private_key_group`                         | `root`                                                                                         | false    |
| `_def_crt_remote_private_key_mode`                          | `0400`                                                                                         | false    |
| `_def_crt_remote_private_key_dir_owner`                     | `root`                                                                                         | false    |
| `_def_crt_remote_private_key_dir_group`                     | `root`                                                                                         | false    |
| `_def_crt_remote_private_key_dir_mode`                      | `0700`                                                                                         | false    |
| `_def_crt_remote_public_key_path`                           | `/root/certs/{{ inventory_hostname }}.{{ _def_crt_cert_suffix }}`                              | false    |
| `_def_crt_remote_public_key_owner`                          | `root`                                                                                         | false    |
| `_def_crt_remote_public_key_group`                          | `root`                                                                                         | false    |
| `_def_crt_remote_public_key_mode`                           | `0400`                                                                                         | false    |
| `_def_crt_remote_public_key_dir_owner`                      | `root`                                                                                         | false    |
| `_def_crt_remote_public_key_dir_group`                      | `root`                                                                                         | false    |
| `_def_crt_remote_public_key_dir_mode`                       | `0700`                                                                                         | false    |
| `_def_crt_remote_ca_public_key_path`                        | `/root/certs/ca.{{ _def_crt_cert_suffix }}`                                                    | false    |
| `_def_crt_remote_ca_public_key_owner`                       | `root`                                                                                         | false    |
| `_def_crt_remote_ca_public_key_group`                       | `root`                                                                                         | false    |
| `_def_crt_remote_ca_public_key_mode`                        | `0400`                                                                                         | false    |
| `_def_crt_remote_ca_public_key_dir_owner`                   | `root`                                                                                         | false    |
| `_def_crt_remote_ca_public_key_dir_group`                   | `root`                                                                                         | false    |
| `_def_crt_remote_ca_public_key_dir_mode`                    | `0700`                                                                                         | false    |
| `_def_crt_remote_ca_chain_cert_path`                        | `/root/certs/ca-chain.{{ _def_crt_cert_suffix }}`                                              | false    |
| `_def_crt_remote_ca_chain_cert_owner`                       | `root`                                                                                         | false    |
| `_def_crt_remote_ca_chain_cert_group`                       | `root`                                                                                         | false    |
| `_def_crt_remote_ca_chain_cert_mode`                        | `0400`                                                                                         | false    |
| `_def_crt_remote_ca_chain_cert_dir_owner`                   | `root`                                                                                         | false    |
| `_def_crt_remote_ca_chain_cert_dir_group`                   | `root`                                                                                         | false    |
| `_def_crt_remote_ca_chain_cert_dir_mode`                    | `0700`                                                                                         | false    |
| `_def_crt_remote_ca_crl_path`                               | `/root/certs/crl.{{ _def_crt_crl_suffix }}`                                                    | false    |
| `_def_crt_remote_ca_crl_owner`                              | `root`                                                                                         | false    |
| `_def_crt_remote_ca_crl_group`                              | `root`                                                                                         | false    |
| `_def_crt_remote_ca_crl_mode`                               | `0400`                                                                                         | false    |
| `_def_crt_remote_ca_crl_dir_owner`                          | `root`                                                                                         | false    |
| `_def_crt_remote_ca_crl_dir_group`                          | `root`                                                                                         | false    |
| `_def_crt_remote_ca_crl_dir_mode`                           | `0700`                                                                                         | false    |

A note on `crt_remote_ca_crl_path`:

Depending on whether `crt_combine_ca_crl_certs` is set to `true`, `crt_remote_ca_crl_path` should either point to a file (when `crt_combine_ca_crl_certs: true`) or to a
directory (when `crt_combine_ca_crl_certs: false`).
On `crt_combine_ca_crl_certs: true` all files specified in `crt_ca_crl_certs` will be combined to a single file placed at `crt_remote_ca_crl_path`. Otherwise all downloaded files will be
placed with their original name into `crt_remote_ca_crl_path`.

Dependencies
------------

This role makes use of the collection [`community.crypto`](https://github.com/ansible-collections/community.crypto) and the collection [`ansible.posix`](https://github.com/ansible-collections/ansible.posix) which are both specified in `collections/requirements.yml`.

Example Playbook
----------------

```
---
- hosts: 'all'
  gather_facts: false
  roles:
    - 'sscheib.generate_ssl_key_pairs'
  vars:
    #
    # mandatory variables
    #

    # fully qualified domain name (FQDN) for the cert
    crt_cert_fqdn: 'webserver.example.com'

    # passphrase for the certificate authority (CA) private key
    crt_ca_priv_key_pass: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          [..]

    # PKI host - this is where the key generation will happen
    crt_pki_host: 'pki.example.com'

    # remote user to connect to the PKI host with
    crt_pki_host_remote_user: 'steffen'

    #
    # optional variables
    #

    # certificate suffixes
    #

    # private key suffix
    crt_priv_key_suffix: 'key.pem'
    
    # certificate signing request (CSR) suffix
    crt_csr_suffix: 'csr.pem'
    
    # certificate (aka public key) suffix
    crt_cert_suffix: 'cert.pem'
    
    # certificate revocation list (CRL) suffix
    crt_crl_suffix: 'crl.pem'

    # certificate authority (CA)
    #

    # root directory of the CA on the PKI host
    crt_ca_root_dir: '/root/ca'
    
    # name of the CA certificate (aka public key)
    crt_ca_cert_name: 'ca.{{ crt_cert_suffix }}'
    
    # name of the CA chain certificate (for intermediate CAs)
    crt_ca_chain_cert_name: 'ca-chain.{{ crt_cert_suffix }}'
    
    # name of the CA private key
    crt_ca_priv_key_name: 'ca.{{ crt_priv_key_suffix }}'
    
    # path of the CA's private keys directory
    crt_ca_priv_key_dir_path: '{{ crt_ca_root_dir }}/private'
    
    # path of the CA's certificates directory
    crt_ca_cert_dir_path: '{{ crt_ca_root_dir }}/certs'
    
    # path of the CA's CSR directory
    crt_ca_csr_dir_path: '{{ crt_ca_root_dir }}/csr'
    
    # path of the CA certificate
    crt_ca_cert_path: '{{ crt_ca_cert_dir_path }}/{{ crt_ca_cert_name }}'
    
    # path of the CA chain certificate (for intermediate CAs)
    crt_ca_chain_cert_path: '{{ crt_ca_cert_dir_path }}/{{ crt_ca_chain_cert_name }}'
    
    # path of the CA private key
    crt_ca_priv_key_path: '{{ crt_ca_cert_dir_path }}/{{ crt_ca_priv_key_name }}'
    
    # whether to fetch the CA certificate
    crt_ca_fetch_ca_cert: true
    
    # whether fo fetch the CA chain certificate (for intermediate CAs)
    crt_ca_fetch_ca_chain_cert: true

    # Certificate Signing Request (CSR)
    #

    # CSR email
    crt_csr_email: 'steffen@example.com'

    # CSR organization name (O)
    crt_csr_org: 'Example Org'

    # CSR organization unit name (OU)
    crt_csr_org_unit: 'IT'

    # CSR country name (C)
    crt_csr_country: 'DE'

    # CSR state name (S)
    crt_csr_state: 'BW'

    # CSR locality name (L)
    crt_csr_loc: 'Home'

    # CSR common name (usually the FQDN)
    crt_csr_common_name: 'webserver.example.com'

    # CSR key usage:
    crt_csr_key_usage:
      - 'digitalSignature'
      - 'keyEncipherment'
      - 'keyAgreement'

    # CSR extended key usage
    crt_csr_extended_key_usage:
      - 'clientAuth'
      - 'serverAuth'

    # Certificate Revocation List (CRL)
    #
    
    # whether to fetch the CRL certificate/s
    crt_ca_fetch_ca_crl_cert: true
    
    # whether to combine the CRLs into one CRL file (in case of multiple CRLs to fetch)
    crt_combine_ca_crl_certs: true
    
    # destination name of the combined CRL files
    crt_crl_dest_name: 'ca-chain.{{ crt_crl_suffix }}'

    # Private Key
    #
    
    # private key type to generate
    crt_priv_key_type: 'RSA'
    
    # private key length/size to generate
    crt_priv_key_size: 4096
    
    # whether to force private key regeneration
    crt_force_priv_key_generation: false
    
    # General
    #
    
    # path on the Ansible controller where to (temporarily) store fetched certificates
    crt_local_dest_path: '/tmp/fetched'
    
    # Permissions of the files inside crt_local_dest_path
    crt_local_dest_path_owner: 'steffen'
    crt_local_dest_path_group: 'steffen'
    crt_local_dest_path_mode: '0400'
    
    # whether to quiet assert
    crt_quiet_assert: false
    
    # SSH port of the PKI host
    crt_pki_host_remote_port: 1905
    
    # whether to remove locally stored temporary certificates (will break idempotency)
    crt_remove_temporary_local_certificates: true

    # path on the managed node where to store fetched certificates

    # private key
    crt_remote_private_key_path: '/root/certs/{{ inventory_hostname }}.key.pem'
    crt_remote_private_key_owner: 'root'
    crt_remote_private_key_group: 'root'
    crt_remote_private_key_mode: '0600'

    # private key directory
    crt_remote_private_key_dir_owner: 'root'
    crt_remote_private_key_dir_group: 'root'
    crt_remote_private_key_dir_mode: '0600'

    # public key
    crt_remote_public_key_path: '/root/certs/{{ inventory_hostname }}.cert.pem'
    crt_remote_public_key_owner: 'root'
    crt_remote_public_key_group: 'root'
    crt_remote_public_key_mode: '0600'

    # private key directory
    crt_remote_public_key_dir_owner: 'root'
    crt_remote_public_key_dir_group: 'root'
    crt_remote_public_key_dir_mode: '0600'

    # certificate authority public key
    crt_remote_ca_public_key_path: '/root/certs/ca.cert.pem'
    crt_remote_ca_public_key_owner: 'root'
    crt_remote_ca_public_key_group: 'root'
    crt_remote_ca_public_key_mode: '0600'

    # certificate authority public key directory
    crt_remote_ca_public_key_dir_owner: 'root'
    crt_remote_ca_public_key_dir_group: 'root'
    crt_remote_ca_public_key_dir_mode: '0600'

    # certificate authority CRL
    crt_remote_ca_crl_path: '/root/certs/ca.crl.pem'
    crt_remote_ca_crl_owner: 'root'
    crt_remote_ca_crl_group: 'root'
    crt_remote_ca_crl_mode: '0600'

    # certificate authority CRL directory
    crt_remote_ca_crl_dir_owner: 'root'
    crt_remote_ca_crl_dir_group: 'root'
    crt_remote_ca_crl_dir_mode: '0600'

    # CA CRL certificates to download
    crt_ca_crl_certs:
      - '/root/ca/crl/ca.crl.pem'
      - '/root/ca/intermediate/intermediate.example.com/crl/intermediate.crl.pem'
...
```


License
-------

GPL v2 or later
