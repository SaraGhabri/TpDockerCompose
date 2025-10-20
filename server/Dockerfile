FROM node:lts-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install --production

COPY . .

EXPOSE 9000


ENV MONGO_URI="mongodb://mongodb:27017/mern"

CMD ["npm", "start"]
