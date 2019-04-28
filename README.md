# kops

##  A.  Setup KOPS on the machine 

    1.  curl -Lo kops https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64

    2.  chmod +x ./kops

    3.  sudo mv ./kops /usr/local/bin/

    
##  B.  Setup kubectl on the machine

    1.  sudo apt-get update && sudo apt-get install -y apt-transport-https

    2.  curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

    3.  echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list

    4.  sudo apt-get update

    5.  sudo apt-get install -y kubectl

##  C.  Configurations befure using KOPS

    1.  ssh-keygen to generate ssh keys if not exists. provide accurate permissions (700 to .ssh and 600 to .ssh/*)
    
    2.  cat id_rsa.pub > authorized_keys; chmod 600 authorized_keys;
    
    3.  Create a route53 domain on AWS -
    
        a.  Multiple clusters can be created by using subdomain as a part of hosted zones
        
        b.  Create a route53 hosted zone using CLI - aws route53 create-hosted-zone --name kubernetesfederatedcluster.com --caller-reference 1
        
                                                              OR
                                                              
        c.  Create a route53 hosted zone using AWS GUI
        
        d.  Edit the nameserver settings from your DNS provider and set the nameservers provided by AWS 
        
        e.  Verify the nameserver settings by running : dig NS kubernetesfederatedcluster.com
        
    4.  Create a S3 bucket to store your state of the cluster - 
    
        aws s3 mb s3://clusters.kubernetesfederatedcluster.com 
        
    5.  Install terraform (OPTIONAL)
    
        TERRAFORM_ZIP_FILE=terraform_0.11.7_linux_amd64.zip
        
        TERRAFORM=https://releases.hashicorp.com/terraform/0.11.7
        
        TERRAFORM_BIN=terraform
        
        wget ${TERRAFORM}/${TERRAFORM_ZIP_FILE}
        
        mv ${TERRAFORM_BIN} /usr/local/bin/${TERRAFORM_BIN}
        
        terraform version
        
        terraform init
        
##  D.  Create cluster using KOPS 

      Using Kops without terraform with default values
    
        export KOPS_STATE_STORE=s3://clusters.kubernetesfederatedcluster.com 
        
        kops create cluster --zones=us-east-1c kubernetesfederatedcluster.com
        
        kops update cluster kubernetesfederatedcluster.com
        
      
      Using KOPS with private hosted zones - 
      
         kops create cluster k8s-clusters.example.com   \
         --node-count=6   \
         --zones=us-east-1c \
         --networking=calico \
         --dns=private \
         --master-count=3

        



    
        
        
