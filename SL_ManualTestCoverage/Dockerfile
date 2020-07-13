

##Node alpine version (find on Dockerhub)
FROM node:alpine3.10

##Open Port
EXPOSE 5030

##Copy
COPY node_sltoken.txt .


##InstallGit
RUN apk add git

##Git clone
RUN rm -rf Game; git clone https://github.com/maheedhar132/Game.git

##Build agent
RUN cd Game &&  npm i slnodejs
RUN cd Game/client &&  npm i slnodejs
RUN cp ./node_sltoken.txt ./Game/client
ARG build
ENV buildNumber=build
RUN ./Game/node_modules/.bin/slnodejs config --tokenfile node_sltoken.txt --appname 'ManualTest' --branch 'test' --build $buildNumber
RUN export SL_BUILD_SESSION_ID=`cat buildSessionId`

##Build
RUN cd Game/client &&  npm install
RUN cd Game &&  npm install
RUN cd Game/client &&  npm run build
RUN cp ./buildSessionId ./Game/client
RUN cd Game/client && ./node_modules/.bin/slnodejs build --tokenfile node_sltoken.txt --buildsessionidfile buildSessionId --instrumentForBrowsers  --workspacepath build --outputpath sl_build --scm none
RUN mv Game/client/build Game/client/old_build
RUN mv Game/client/sl_build Game/client/build


##Run the application
RUN npm install forever -g
RUN forever -v
RUN if forever list | grep 'sl'; then forever stop sl; fi
RUN cd Game && forever start -a --uid sl -c 'npm start' ./