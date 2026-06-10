### writing multistage docker file for nodejs app

FROM node:20-alpine as builder
WORKDIR /app
COPY package*.json ./
RUN npm install

##### Stage 2: Build the app

FROM node:20-slim
WORKDIR /app
# Copy the built files from the builder stage
COPY --from=builder /app/node_modules ./node_modules
COPY . .
EXPOSE 3000
CMD ["npm", "start"]