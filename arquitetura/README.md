# Arquitetura do Sistema

## Visão Geral

O projeto é uma **SPA + API REST** com separação clara entre front-end e back-end.

```
┌─────────────────────────────────────────────────────────┐
│                    CLIENTE (Navegador)                  │
│                                                         │
│  ┌─────────────────────────────────────────────────┐    │
│  │              Vue 3 + Vite (SPA)                 │    │
│  │                                                 │    │
│  │  Views → Components → Stores (Pinia)            │    │
│  │                     ↓                           │    │
│  │              Vue Router (navegação)             │    │
│  └──────────────────────┬──────────────────────────┘    │
│                         │ HTTP/JSON                     │
└─────────────────────────┼───────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│                SERVIDOR (Django)                        │
│                                                         │
│  ┌─────────────────────────────────────────────────┐    │
│  │           Django REST Framework                 │    │
│  │                                                 │    │
│  │  Serializers → Views/ViewSets → Models          │    │
│  │                                    ↓            │    │
│  │                            PostgreSQL           │    │
│  └─────────────────────────────────────────────────┘    │
│                                                         │
│  ┌─────────────────────────────────────────────────┐    │
│  │           Django Admin (painel de gestão)       │    │
│  └─────────────────────────────────────────────────┘    │
│                                                         │
│  Hospedagem: Fabroku                                    │
└─────────────────────────────────────────────────────────┘
```

---

## Camadas

### Front-End (Vue 3)

```
src/
├── assets/                
├── components/
│   ├── layout/            
├── composables/           
├── data/                  
├── router/                
├── stores/              
├── views/                 
├── App.vue   
├── .env           
└── main.js               
```

### Back-End (Django)

```
backend/
│
├── manage.py
└── .env
```

---

## Fluxo de Requisição

```
1. Usuário clica em "Começar Agora"
         │
         ▼
2. Vue Router navega para /anos
         │
         ▼
3. AnosView.vue chama a API
   GET /api/anos/
         │
         ▼
4. Django recebe a requisição
   → View/ViewSet processa
   → Model consulta PostgreSQL
   → Serializa resposta JSON
         │
         ▼
5. Vue recebe JSON e renderiza os cards
```

---

## Rotas do Frontend

```
/                                          → HomeView
/:pathMatch(.*)*                           → NotFoundView
```

## Endpoints da API Django

```

```

---

## Sistema de Blocos

Cada atividade é composta por blocos ordenados:

| Tipo | JSON conteudo |
|------|---------------|


---