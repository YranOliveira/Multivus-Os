# 🚀 MULTIVUS - Sistema de Gestão Empresarial

Sistema completo de gestão empresarial com ordem de serviços, controle financeiro, estoque, clientes e integração com EFI Pay e WhatsApp.

## 📋 Índice

- [Características](#características)
- [Tecnologias Utilizadas](#tecnologias-utilizadas)
- [Pré-requisitos](#pré-requisitos)
- [Instalação Ubuntu](#instalação-ubuntu)
- [Instalação Windows](#instalação-windows)
- [Configuração](#configuração)
- [Uso](#uso)
- [API](#api)
- [Contribuição](#contribuição)
- [Licença](#licença)

## ✨ Características

### 🏢 **Multi-empresa**
- Sistema multi-tenant
- Gestão independente por empresa
- Super administrador global
- Planos de assinatura

### 📱 **PWA (Progressive Web App)**
- Funciona offline
- Instalável no celular/desktop
- Notificações push
- Cache inteligente

### 💳 **Integração EFI Pay**
- PIX automático
- Boletos
- Cartão de crédito
- Webhook em tempo real

### 📞 **WhatsApp Business**
- Envio automático de mensagens
- Templates personalizáveis
- Integração com ordens de serviço
- Notificações de pagamento

### 🗄️ **Gestão Completa**
- Ordem de serviços
- Controle de estoque
- Gestão financeira
- Cadastro de clientes
- Relatórios em PDF/PNG

## 🛠️ Tecnologias Utilizadas

### **Frontend**
- **Next.js 14** - Framework React com App Router
- **TypeScript** - Tipagem estática
- **Tailwind CSS** - Framework CSS utilitário
- **Radix UI** - Componentes acessíveis
- **Lucide React** - Ícones modernos

### **Backend**
- **Next.js API Routes** - API RESTful
- **PostgreSQL 17** - Banco de dados relacional
- **JWT** - Autenticação segura
- **bcryptjs** - Hash de senhas

### **Integrações**
- **EFI Pay API** - Gateway de pagamento
- **WhatsApp Baileys** - API não oficial do WhatsApp
- **jsPDF** - Geração de PDFs
- **Canvas API** - Geração de imagens

### **Infraestrutura**
- **PM2** - Gerenciador de processos
- **Nginx** - Proxy reverso
- **Docker** - Containerização
- **Let's Encrypt** - SSL gratuito

## 📋 Pré-requisitos

### **Sistema Operacional**
- Ubuntu 20.04+ (recomendado: 22.04 LTS)
- Windows Server 2019+
- CentOS 8+

### **Hardware Mínimo**
- **CPU**: 2 cores
- **RAM**: 4GB
- **Disco**: 20GB SSD
- **Rede**: 100Mbps

### **Hardware Recomendado**
- **CPU**: 4+ cores
- **RAM**: 8GB+
- **Disco**: 50GB+ SSD
- **Rede**: 1Gbps

## 🐧 Instalação Ubuntu

### **1. Instalação Automática (Recomendada)**

\`\`\`bash
# Baixar e executar script de instalação
curl -fsSL https://raw.githubusercontent.com/multivus/install/main/ubuntu-install.sh | sudo bash
\`\`\`

### **2. Instalação Manual**

#### **2.1. Atualizar Sistema**
\`\`\`bash
sudo apt update && sudo apt upgrade -y
\`\`\`

#### **2.2. Instalar Node.js 20**
\`\`\`bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo bash -
sudo apt-get install -y nodejs
\`\`\`

#### **2.3. Instalar PostgreSQL 17**
\`\`\`bash
# Adicionar repositório
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
echo "deb http://apt.postgresql.org/pub/repos/apt/ $(lsb_release -cs)-pgdg main" | sudo tee /etc/apt/sources.list.d/pgdg.list

# Instalar
sudo apt update
sudo apt install -y postgresql-17 postgresql-client-17 postgresql-contrib-17

# Configurar
sudo systemctl start postgresql
sudo systemctl enable postgresql
sudo -u postgres psql -c "ALTER USER postgres PASSWORD 'multivus2024';"
sudo -u postgres createdb multivus_db
\`\`\`

#### **2.4. Instalar PM2 e Nginx**
\`\`\`bash
sudo npm install -g pm2
sudo apt install -y nginx
\`\`\`

#### **2.5. Clonar e Configurar Projeto**
\`\`\`bash
# Clonar repositório
git clone https://github.com/seu-usuario/multivus.git
cd multivus

# Instalar dependências
npm install

# Configurar ambiente
cp .env.example .env
nano .env

# Executar migrações
npm run db:migrate
npm run db:seed

# Construir aplicação
npm run build

# Iniciar com PM2
pm2 start ecosystem.config.js
pm2 save
pm2 startup
\`\`\`

#### **2.6. Configurar Nginx**
\`\`\`bash
sudo nano /etc/nginx/sites-available/multivus
\`\`\`

```nginx
server {
    listen 80;
    server_name seu-dominio.com;
    
    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }
}
