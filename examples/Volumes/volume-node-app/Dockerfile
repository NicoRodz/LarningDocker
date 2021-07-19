FROM node:14

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

EXPOSE 80

VOLUME ["/app/feedback"]
# This is the path inside of the container which should be referenced outside of it, on a shared volume.

CMD [ "node", "server.js" ]