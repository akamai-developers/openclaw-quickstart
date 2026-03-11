# OpenClaw Linode VM Terraform Setup

This Terraform configuration creates a Linode VM with security hardening and automated setup for openclaw/clawdbot.

## Prerequisites

1. [Terraform](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli) installed
2. Create an [Akamai Cloud account here](http://login.linode.com/signup?promo=akm-dev-git-300-31126-M055) with API token (we have a $300 credit just for you)  
3. SSH key pair generated

## Setup

1. **Generate SSH keys** (if you don't have them):
   ```bash
   ssh-keygen -t ed25519 -C "openclaw@example.com" -f ~/.ssh/openclaw
   ```

2. **Set your Linode API token**:
   ```bash
   export LINODE_TOKEN="your-linode-api-token"
   ```

3. **Create a `terraform.tfvars` file**:
   ```hcl
   linode_token     = "your-linode-api-token"
   public_key_path  = "~/.ssh/openclaw.pub"
   allowed_ssh_cidrs = ["YOUR_IP/32"]  # Replace with your IP
   region           = "us-east"  # or your preferred region
   instance_type    = "g6-nanode-1"  # or your preferred plan
   ```

## Usage

```bash
# Initialize Terraform
terraform init

# Plan the deployment
terraform plan

# Apply the configuration
terraform apply

# View outputs (including generated root password)
terraform output -json

# SSH as openclaw user
ssh openclaw@$(terraform output -raw instance_ip)
```

## Notes

- The firewall allows SSH only from the specified CIDR blocks
- Password authentication is disabled via cloud-init
- Root login is restricted to key-based auth only
- The `openclaw` user has passwordless sudo access

## Variables
| Variable | Description | Default |
|----------|-------------|---------|
| `linode_token` | Linode API token | Required |
| `public_key_path` | Path to SSH public key | Required |
| `allowed_ssh_cidrs` | CIDR blocks for SSH access | `["0.0.0.0/0"]` |
| `instance_label` | VM label | `"openclaw-vm"` |
| `region` | Linode region | `"us-east"` |
| `instance_type` | VM plan | `"g6-nanode-1"` |
| `root_password` | Root password (auto-generated if empty) | `""` |

## Outputs
| Output | Description |
|--------|-------------|
| `instance_ip` | Public IP of the VM |
| `ssh_command` | SSH command to connect |
| `root_password` | Root password (sensitive) |
| `firewall_id` | Firewall ID |


## openclaw configuration
openclaw (Clawdbot) is installed and ready to use out-of-the-box on the VM. 

SSH to the VM as `openclaw` user

```bash
ssh openclaw@$VMIP
```
and run 

```bash
clawdbot onboard --install-daemon
```

to begin the onboarding process - follow [upstream docs](https://docs.molt.bot/start/getting-started)
