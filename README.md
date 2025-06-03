Get DynamoDB To Make Forms - Linux Bash Steps

#choose location localhost

cd ..

ls -l

#Make sure you are in the home folder
#Save these files in the home folder use touch and vim, nano or micro to make the files and save them

"""
#Step one create this .py file and name it setup_dynamodb.py
#This code creates the folders needed for dynamodb to run with the permissions needed
"""

import os

def setup_dynamodb_local():
    # Define the directory path
    dynamodb_data_path = "/home/dynamodb/data"
    
    try:
        # Create the directory with the -p equivalent (no error if it exists)
        os.makedirs(dynamodb_data_path, exist_ok=True)
        print(f"Directory created or already exists: {dynamodb_data_path}")
        
        # Change permissions to chmod 777
        os.chmod(dynamodb_data_path, 0o777)
        print(f"Permissions set to 777 for: {dynamodb_data_path}")
    except PermissionError:
        print("Permission denied: Run this script with sufficient privileges (e.g., as sudo).")
    except Exception as e:
        print(f"An error occurred: {e}")

if __name__ == "__main__":
    setup_dynamodb_local()

"""
#Now make the yaml or .yml file  and save as docker-compose.yml
#Make yml file named docker-compose.yml
"""

version: '3.8'
services:
  dynamodb-local:
    command: "-jar DynamoDBLocal.jar -sharedDb -dbPath ./data"
    image: "amazon/dynamodb-local:latest"
    container_name: dynamodb-local
    ports:
      - "8002:8000"  # Maps host port 8002 to container port 8000
    restart: always
    volumes:
      - "./data:/home/dynamodblocal/data"
    working_dir: /home/dynamodblocal

  dynamodb-admin:
    image: "aaronshaf/dynamodb-admin"
    container_name: dynamodb-admin
    depends_on:
      - dynamodb-local
    restart: always
    ports:
      - "8001:8001"
    environment:
      - DYNAMO_ENDPOINT=http://dynamodb-local:8000
      - AWS_REGION=ap-us-east-2

#Run the python with sudo

sudo setup_dynamodb.py

#Download dynamodb docker

sudo docker pull amazon/dynamodb-local

#bash now

sudo docker-compose up

#open new terminal window

sudo docker-compose up -d

sudo docker ps

docker-compose --version

ls -l![image](https://github.com/user-attachments/assets/20cc026f-3a82-4625-ae4f-419446d47930)
