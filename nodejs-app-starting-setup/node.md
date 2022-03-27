# Note

build an image
```
#Base on node image
FROM node 

#container default work dir is /root 
#set our own working direction
WORKDIR /app

#Our app setting
#copy folder to container folder
#copy current foler to /app(our workdir) inside container 
COPY . /app

#install all package
RUN npm install 

#export to local machine
EXPOSE 80 

#cmd will execute when container is runnning
#this won't run when build the image
#an extrea layer will be added when container is running
CMD ["node","server.js"]
```

### What happen if we changed the source code
> Def: Image is layer-based which means each instructions is a image layer. Each layer has its own cache.When rebuild the image,there are nothing changed,it'll build the image by using the cache data not re-run the instruction

For example: We chanage the source code below
```js
app.get('/', (req, res) => {
  res.send(`
    <html>
      <head>
        <link rel="stylesheet" href="styles.css">
      </head>
      <body>
        <section>
          <h2>My Course Goal</h2>
          <h3>${userGoal}!!!!!!!</h3> //Change here!!!!
        </section>
        <form action="/store-goal" method="POST">
          <div class="form-control">
            <label>Course Goal</label>
            <input type="text" name="goal">
          </div>
          <button>Set Course Goal</button>
        </form>
      </body>
    </html>
  `);
});
```

Building image process:
```
FROM node  #using caching
WORKDIR /app #using caching
COPY . /app #docker detached something is changed,re-run this layer
RUN npm install #Coz downer layer re-run,it need to re-run too
 
EXPOSE 80  #Coz downer layer re-run,it need to re-run too

#an extrea layer will be added when container is running
CMD ["node","server.js"]
```

Simple Optimization:
> we are not modified any dependency/packer, so we not need to run npm install again

```
FROM node  #using caching
WORKDIR /app #using caching

#copying the package file firest
COPY package.json ./app
#run npm install
RUN npm install #Coz downer layer re-run,it need to re-run too
 
#if we change the source code,the layer above will use their cache
COPY . /app #docker detached something is changed,re-run this layer

EXPOSE 80  #Coz downer layer re-run,it need to re-run too

#an extrea layer will be added when container is running
CMD ["node","server.js"]
```