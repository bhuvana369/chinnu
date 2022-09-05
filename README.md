# chinnu
provider "google" {

project = "unified-poet-356510"
region = "us-central1"
}

resource "google_compute_global_address" "default" {
  name = "global-appserver-ip"
}

resource "google_compute_network" "vpc_network" {
  project                 = "my-project-name"
  name                    = "vpc-network"
  auto_create_subnetworks = true
  mtu                     = 1460
}

variable "VC_NAME" {
  type = string
}

# Create a single Compute Engine instance
resource "google_compute_instance" "default" {
  name         = var.VC_NAME
  machine_type = "f1-micro"
  zone         = "us-central1-a"
  tags         = ["ssh"]

  metadata = {
    enable-oslogin = "TRUE"
  }
  boot_disk {
    initialize_params {
      image = "projects/ml-images/global/images/c0-deeplearning-common-cu110-v20220806-debian-10"
    }
  }
metadata_startup_script = "sudo apt update -y; jupyter notebook --ip=0.0.0.0 --port=8989 --no-browser &"

  network_interface {
    network = "default"
  
    access_config {
      # Include this section to give the VM an external IP address
    }
  }
}
