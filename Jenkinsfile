pipeline {
    agent any
    stages {
        stage('test Azure cred') {
            steps {
                withCredentials([azureServicePrincipal('61f78e17-f6a2-4ac2-a4be-c4edfa1a20d6')]) {
                    sh 'az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID'
                }
                script {
                    sh label: '', script: '''
                    az account show
                    RESOURCE_GROUP_NAME=tstate
                    STORAGE_ACCOUNT_NAME=tstate
                    CONTAINER_NAME=tstate
                    
                    # Create resource group
                    az group create --name $RESOURCE_GROUP_NAME --location eastus
                    
                    # Create storage account
                    az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob
                    
                    # Get storage account key
                    ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query [0].value -o tsv)
                    
                    # Create blob container
                    az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY
                    
                    echo "storage_account_name: $STORAGE_ACCOUNT_NAME"
                    echo "container_name: $CONTAINER_NAME"
                    echo "access_key: $ACCOUNT_KEY"
                    az logout
                    '''
                }
            }
        }
    }
}
