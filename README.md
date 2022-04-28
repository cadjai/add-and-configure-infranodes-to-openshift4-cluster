Role Name
=========

 A utility playbook to add and configure infrastructure nodes for an OpenShift Container Platform 4.x and also to help relocate infra workload to newly provisioned infra nodes. 

Requirements
------------

This has been tested on AWS cloud regions.

Role Variables
--------------

- openshift_cli: Openshift client binary used to interact with the cluster api (default to 'oc')
- ocp_cluster_user: The name of the cluster-admin user used to perform the various actions against the cluster.
- ocp_cluster_user_password: The password of the cluster-admin user used to perform the various actions against the cluster.
- ocp_cluster_console_url: The URL of the API the cluster these actions are being applied to.
- ocp_cluster_console_port: The port on which the cluster api is listening (default to 6443)
- aws_region: The AWS region the resource is to be provisioned in.
- aws_az: The AWS Availability Zone to to provision the node in.
- role: The Kubernetes role to apply to the node. Default to infra but can also be set to worker or any other string.
- type: The Kubernetes type to apply to the node. Default to infra but can also be set to worker or any other string.
- sg: The AWS security group to apply to the node once provisioned. It is assumed that the security group already exists. Default to worker_sg since that is created at cluster deployment.
- profile: The AWS IAM profile to apply to the node once provisioned. It is assumed that the IAM profile already exists. Default to worker since that is created at cluster deployment.
- replica_count: The number of node to provision. Default to 1 given that we are provisioning a node per AZ.
- block_size: The size of the root partition for the RHCOS instance . Default to 120 GB.
- volume_type: The type of persistence volume to use to support infrastructure resource persistence. Default to gp2.
- relocate_ingress_controller: Indicates whether to move Ingress Controller from currently deployed worker nodes to the new infra nodes. Default to true.
- relocate_image_registry: Indicates whether to move internal Image registry from currently deployed worker nodes to the new infra nodes. Default to true.
- relocate_monitoring: Indicates whether to move cluster monitoring components from currently deployed worker nodes to the new infra nodes. Default to true.
- relocate_logging: Indicates whether to move cluster logging components from currently deployed worker nodes to the new infra nodes. Default to true.


Dependencies
------------


Example Playbook
----------------

    - hosts: localhost
      become: yes
      vars_files:
        - 'vars/vault.yml'
        - 'vars/global.yml'
      pre_tasks:
        - name: Authenticate with the API
          command: >
            {{ openshift_cli }} login \
              -u {{ ocp_cluster_user }} \
              -p {{ ocp_cluster_user_password }} \
              --insecure-skip-tls-verify=true {{ ocp_cluster_console_url }}:{{ ocp_cluster_console_port | d('6443', true) }}
          register: login_out 
      roles:
         - { role: add-and-config-infranodes }

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
