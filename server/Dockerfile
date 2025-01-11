FROM node:lts-alpine


WORKDIR /app

COPY package*.json ./


RUN npm install


COPY . .

RUN npm install -g serve

CMD ["node", "bin/www"]

EXPOSE 5000