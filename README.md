# Job Portal Application
This is a flask application implements the job portal usecases 

###### Applicant specific endpoints
- [X] Applicant Registration
- [X] Get complete list of applicants registered
- [ ] Applicants token based login
- [ ] Applicants logout
- [ ] Applicant checking for job applied status
- [ ] Applicants retrieving list of jobs applied
- [ ] Apply to a job

###### Job specific endpoints
- [ ] Post a new job requirement to portal
- [ ] Get the details of posted job
- [ ] Get all the list of jobs posted
- [ ] Get the list of all applicants applied to the job

###### Employee / HR operations
- [ ] Employee login token based
- [ ] Hire an applicant to employee
- [ ] Employee profile management
- [ ] Employee logout

# Step1: Run mongodb instance in docker with the following way

###### run with username and password configured
docker run -d -it --rm --name jp-mongodb -p 27017:27017 -e MONGO_INITDB_ROOT_USERNAME="root" -e MONGO_INITDB_ROOT_PASSWORD="root-rusteez" mongo
###### run without username and password
docker run -d -it --rm --name jp-mongodb -p 27017:27017  mongo

# Step2: Build the python flask application, tag it and push the image to the docker hub
docker build -t flaskapp:jp --build-arg requirements="requirements.txt" --build-arg workspace="jobPortal" -f Dockerfile .

docker run -d -it --rm --name jobportal \
 -e FLASK_APP='jobportal' -e FLASK_ENV=development -e FLASK_RUN_HOST='0.0.0.0' -e FLASK_RUN_PORT=5000 \
 -e MONGO_HOST='jp-mongodb' -e MONGO_PORT=27017 \
 -p 5000:5000 \
 flaskapp:jp

docker tag flaskapp:jp pravsp/galaxies:pyflask-jobportal

docker push pravsp/galaxies:pyflask-jobportal

# Step3: Create docker network to connect the job portal application and mongodb container

docker network create jp-network
docker network connect jp-network jobportal
docker network connect jp-network jp-mongodb

# Step4: Run the curl commands

curl -XGET http://localhost:5000/applicants

curl --header "Content-Type: application/json" \
 --request POST \
 --data '{"username": "aruncm", "password": "test123", "fname": "Arun", "lname": "CM", "email": "aruncm@abc.corp", "phone": "123789"}' \
 http://localhost:5000/applicant/register

