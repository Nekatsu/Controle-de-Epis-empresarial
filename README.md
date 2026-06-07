# Controle-de-Epis-empresarial
Sistema de gerenciamento de estoque de Equipamentos de Proteção Individual (EPIs) self-hosted, com banco de dados local e acesso via rede interna.

	Sobre o projeto

Este projeto consiste na implantação e configuração do **Snipe-IT** — software open source originalmente voltado para gestão de ativos de TI — adaptado para controle de estoque de EPIs de uma empresa.

O sistema permite:

- Cadastrar EPIs por categoria (capacete, luva, bota, óculos, etc.)
- Registrar entrega e devolução de equipamentos por funcionário
- Controlar validade do CA (Certificado de Aprovação) de cada EPI
- Gerar relatórios de estoque disponível e histórico de movimentações
- Acessar o sistema de qualquer dispositivo na rede local (PC, celular, tablet)

---

	Tecnologias utilizadas

- **[Snipe-IT](https://snipeitapp.com/)** — sistema de gestão de ativos open source
- **Docker + Docker Compose** — containerização da aplicação
- **MariaDB** — banco de dados relacional para persistência dos dados
- **PHP / Laravel** — stack do Snipe-IT

---

	Como executar localmente

	Pré-requisitos

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) instalado

	Passo a passo

 1. Clone o repositório**
```bash
git clone https://github.com/seu-usuario/controle-epis.git
cd controle-epis
```

 2. Configure o arquivo de ambiente**
```bash
  Renomeie o arquivo de exemplo
cp .env.example .env
```
Edite o `.env` e preencha os campos necessários (veja a seção de configuração abaixo).

 3. Gere a APP_KEY**
```bash
docker compose run --rm app php artisan key:generate --show
```
Cole a chave gerada no campo `APP_KEY=` do arquivo `.env`.

 4. Suba os containers**
```bash
docker compose up -d
```

 5. Acesse no navegador**
```
http://localhost:8000
```

Na primeira vez, o sistema vai solicitar a criação do usuário administrador.

---

  -- Configuração

Crie um arquivo `.env` na raiz do projeto com base no `.env.example`:

```env
APP_ENV=production
APP_DEBUG=false
APP_URL=http://localhost:8000
APP_KEY= 'cole a app_key do passo 3 aqui'

DB_CONNECTION=mysql
DB_HOST=db
DB_DATABASE=snipeit
DB_USERNAME=snipeit
DB_PASSWORD=sua_senha_aqui

APP_TIMEZONE=America/Sao_Paulo
APP_LOCALE=pt-BR
```

---

 	 Estrutura do projeto

```
controle-epis/
¹ docker-compose.yml   # configuração dos containers
² .env.example         # modelo de variáveis de ambiente
³ README.md
```

---

	 Adaptações realizadas

Por padrão o Snipe-IT é voltado para TI. As seguintes adaptações foram feitas para uso com EPIs:

| Conceito no Snipe-IT | Adaptado para |
|---|---|
| Assets (Ativos) | EPIs individuais (ex: Capacete #001) |
| Asset Models | Modelos de EPI (ex: Capacete MSA V-Gard) |
| Categories | Categorias de proteção (cabeça, mãos, pés...) |
| Manufacturers | Fabricantes de EPI (MSA, 3M, Honeywell...) |
| Checkout / Checkin | Entrega / Devolução ao funcionário |

  Campos personalizados adicionados

- Número do CA (Certificado de Aprovação)
- Validade do CA
- Vida útil do equipamento
- Tamanho / numeração
- Data de entrega ao funcionário

---

  Backup do banco de dados

Para realizar backup manual:

```bash
docker exec snipeit-epi-db-1 mysqldump -u snipeit -psnipeit123 snipeit > backup_$(date +%Y%m%d).sql
```

Recomenda-se executar semanalmente e armazenar em local seguro.

---

  Acesso pela rede local

Para acessar de outros dispositivos na mesma rede Wi-Fi:

1. Descubra o IP do servidor: `ipconfig` (Windows)
2. Atualize o `APP_URL` no `.env` com o IP encontrado
3. Reinicie com `docker compose down && docker compose up -d`
4. Acesse pelo celular/tablet: `http://192.168.x.x:8000`

---

  Licença

Este projeto utiliza o [Snipe-IT](https://github.com/snipe/snipe-it), distribuído sob a licença **AGPL-3.0**.

---

> Projeto desenvolvido para gerenciamento interno de EPIs.
