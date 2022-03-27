#Base on node image
FROM node 

#container default work dir is /root 
#set our own working direction
WORKDIR /app

COPY package.json /app
#Our app setting
#copy folder to container folder
#copy current foler to /app(our workdir) inside container 
#install all package
RUN npm install 

COPY . /app

#export to local machine
EXPOSE 80 

#cmd will execute when container is runnning
#this won't run when build the image
CMD ["node","server.js"]
