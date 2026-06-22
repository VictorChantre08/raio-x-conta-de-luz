# ⚡ Raio-X da Conta de Luz


**Descubra quais aparelhos realmente pesam na sua conta de luz — sem comprar nenhum medidor.**

A conta de energia chega todo mês como um número só. A maioria das
pessoas não sabe **quais aparelhos** puxam mais energia, porque medir
isso de verdade exigiria comprar um medidor para cada tomada da casa.

Este projeto resolve isso por estimativa: cruza a potência média de
cada aparelho, o tempo de uso informado pelo usuário, a tarifa real da
distribuidora local (resolvida automaticamente pelo CEP) e o acréscimo
da bandeira tarifária vigente da ANEEL — tudo sem precisar de hardware
nenhum.

---

## 🖥️ A interface

A aplicação tem três blocos principais:

1. **Bandeira tarifária do mês** — card com a cor e o acréscimo vigente
   (verde, amarela, vermelha patamar 1 ou 2), com a opção de simular
   outras bandeiras
2. **Tarifa automática por CEP** — o usuário digita o CEP e a tarifa da
   distribuidora local já vem preenchida
3. **Ranking de vilões** — lista os aparelhos do maior para o menor
   consumidor, com gráfico de barras e dicas de economia personalizadas

```
┌─────────────────────────────────────────┐
│  🟡 Bandeira Amarela — Maio 2026          │
│  +R$ 1,885 a cada 100 kWh consumidos       │   ← acréscimo aplicado
├─────────────────────────────────────────┤
│  📍 CEP → Santos, SP → Enel SP (R$0,95)    │   ← tarifa automática
├─────────────────────────────────────────┤
│  Consumo: 427,6 kWh   Custo: R$ 414,30     │
├─────────────────────────────────────────┤
│  🥇 Ar-condicionado 9.000 BTU   R$ 209,27  │   ← ranking de vilões
│  🥈 Geladeira frost-free         R$ 104,64  │
│  🥉 Chuveiro elétrico             R$ 79,93   │
└─────────────────────────────────────────┘
```

## 🧮 A lógica por trás do cálculo

```
kWh consumido = (potência em Watts × horas de uso/dia × dias do período) / 1000
custo em R$   = kWh × (tarifa da distribuidora + acréscimo da bandeira tarifária)
```

Simples na fórmula, mas a parte interessante é juntar **três fontes de
dado real** num só lugar: catálogo de aparelhos, tarifa por
distribuidora, e bandeira tarifária da ANEEL — algo que a maioria das
calculadoras de consumo por aí não faz de forma integrada.

## 🗂️ Os repositórios

Este é o repositório de apresentação. O projeto é dividido em dois
repositórios técnicos, desacoplados como um sistema real:

| Repositório | O que é | Stack |
|---|---|---|
| [`energia-raiox-backend`](https://github.com/VictorChantre08/energia-raiox-backend) | API REST com a lógica de cálculo, catálogo de aparelhos, integração com ViaCEP e bandeiras da ANEEL | Node.js + Express |
| [`energia-raiox-frontend`](https://github.com/VictorChantre08/energia-raiox-frontend) | Interface web que consome a API | Next.js + TypeScript + Tailwind |

## 🔌 Principais funcionalidades

- ✅ Catálogo com 48 aparelhos comuns, organizados por categoria (climatização, TVs, cozinha, banho, informática, iluminação)
- ✅ Tarifa automática por CEP via integração com ViaCEP + tabela de 27 distribuidoras brasileiras
- ✅ Bandeira tarifária vigente (verde/amarela/vermelha) com simulação manual
- ✅ Ranking dos maiores consumidores ("vilões da conta"), com percentual e gráfico
- ✅ Dicas de economia personalizadas, com simulação de "e se eu usar 1h menos por dia"

## 🛠️ Como rodar o projeto completo localmente

```bash
# 1. Backend
git clone https://github.com/VictorChantre08/energia-raiox-backend.git
cd energia-raiox-backend
npm install
npm run dev          # roda em http://localhost:3001

# 2. Frontend (em outro terminal)
git clone https://github.com/VictorChantre08/energia-raiox-frontend.git
cd energia-raiox-frontend
cp .env.local.example .env.local
npm install
npm run dev          # roda em http://localhost:3000
```

Acesse `http://localhost:3000` com os dois servidores no ar.

## 🧠 Decisões de arquitetura

- **Frontend e backend desacoplados** — o frontend não sabe (nem
  precisa saber) que o backend é Node.js; poderia ser reescrito em
  qualquer linguagem sem quebrar o client
- **Camada de serviços separada das rotas** — a lógica de cálculo
  (`consumptionService.js`) não depende do Express, o que facilita
  testes e reuso futuro (ex: um job agendado)
- **Tipos TypeScript compartilhando o shape da API** — o frontend tem
  os tipos espelhando exatamente o que o backend retorna, evitando
  inconsistência entre as duas pontas

## 🛣️ Próximos passos possíveis

- [ ] Autenticação e persistência (Supabase) para salvar a configuração da casa
- [ ] Job agendado para atualizar a bandeira tarifária automaticamente via dados abertos da ANEEL
- [ ] App mobile reaproveitando a mesma API
- [ ] Relatório mensal exportável em PDF

---

**Sobre o projeto:** construído como exercício de portfólio para
explorar um problema real do dia a dia brasileiro — entender a própria
conta de luz — com dados genuínos do setor elétrico nacional (ANEEL,
distribuidoras, CEP), em vez de um clone genérico de todo-list.



🔗 **[Acesse o app ao vivo](https://energia-raiox-front.vercel.app)**
