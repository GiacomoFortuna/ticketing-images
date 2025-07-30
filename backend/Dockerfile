# Usa una versione LTS stabile di Node
FROM node:20

# Crea la cartella di lavoro nel container
WORKDIR /app

# Copia solo i file di dipendenze (package.json + package-lock.json se presente)
COPY source/package*.json ./

# Installa solo le dipendenze necessarie per la produzione
RUN npm install --production

# Copia tutto il codice del backend nella directory di lavoro
COPY source/ .

# Rimuovi .git se presente (opzionale ma consigliato per immagini più leggere)
RUN rm -rf .git

# Espone la porta su cui il backend è in ascolto
EXPOSE 3001

# Comando per avviare il backend
CMD ["node", "index.js"]

