# 🏢 Projeto Empresa Momento — Auditoria & Analytics em MongoDB
> **Status do Projeto:** Concluído 🚀  
> **Autor:** Cauan Morária

Sistema completo de auditoria, analytics e inteligência de negócios utilizando MongoDB.

✔ Consultas básicas
✔ Agregações avançadas
✔ Auditoria de dados
✔ Simulações empresariais
✔ Dashboard consolidado

![MongoDB](https://img.shields.io/badge/MongoDB-%234ea94b.svg?style=for-the-badge&logo=mongodb&logoColor=white)
![NoSQL](https://img.shields.io/badge/Database-NoSQL-blue?style=for-the-badge)

## 🎯 Sobre o Projeto
Este repositório contém a resolução completa do desafio prático de banco de dados NoSQL para a **Empresa Momento**. O objetivo foi realizar uma auditoria profunda na base de dados da empresa, cobrindo desde métricas básicas de RH e finanças até agregações complexas de desempenho de vendas e otimização de consultas.

Como abordagem de desenvolvimento, o projeto foi dividido em sprints de complexidade (Nível 1 ao Nível 10), garantindo a integridade dos dados através de validações prévias antes de qualquer operação de escrita ou remoção.

## 🗄️ Estrutura do Banco

Coleções utilizadas:

- employees
- departments
- offices
- sales

Relacionamentos:

departments → offices
employees → departments
sales → employees

## 🚀 Tecnologias Utilizadas

- MongoDB
- MongoDB Compass
- Aggregation Framework
- NoSQL
- Git
- GitHub

---

## 🧭 Níveis
* 🛠️ Nível 1: Conhecendo a Empresa
* 📊 Nível 2: Análise Financeira Básica
* 👥 Nível 3: Recursos Humanos
* 📦 Nível 4: Operações e Escritórios
* 📈 Nível 5: Produtos e Vendas
* 🔄 Nível 6: Operações de Atualização
* 🔮 Nível 7: Análise Avançada com Agregações
* ⚡ Nível 8: Desafios e Otimização
* 🥷 Nível 9: Desafios Ninja
* 🏢 Nível 10: Casos Práticos e Simulações


---

# 🛠️ Nível 1: Conhecendo a Empresa

### 1.1 — Inclua suas próprias informações no departamento de Tecnologia da empresa

**Query Executada:**
```javascript
db.employees.insertOne({
  "firstName": "Cauan",
  "lastName": "Morária",
  "birthDate": "2006-06-13",
  "hireDate": "2026-03-13",
  "salary": 0,
  "departmentId": ObjectId("85992103f9b3e0b3b3c1fe74"),
  "role": "Owner",
  "office": "São Paulo"
})
```

### Resultado

Inserido com sucesso!

**ID:** `ObjectId("6a2076e033da2b4f77994412")`

---

## 1.2 — Quantos funcionários temos ao total na empresa?

### Comando MongoDB

```javascript
db.employees.countDocuments({})
```

### Resultado

**25 funcionários**

---

## 1.3 — Quantos funcionários trabalham especificamente no Departamento de Tecnologia?

### Comando MongoDB

```javascript
db.employees.countDocuments({
  departmentId: ObjectId("85992103f9b3e0b3b3c1fe74")
})
```

### Resultado

**3 funcionários**

> Alexander Hunold, David Austin, Diana Lorentz

---

## 1.4 — Liste todos os departamentos que existem na empresa. Quantos são?

### Comando MongoDB

```javascript
db.departments.find({}, { name: 1, _id: 0 })
```

### Resultado

| Departamento |
|-------------|
| Executive |
| Sales |
| Marketing |
| Finance |
| Technology |
| Human Resources |
| Data |
| Innovations |
| Operações LATAM |

**Total: 9 departamentos**

---

## 1.5 — Quantos escritórios a Momento possui? Em quais países?

### Comando MongoDB

```javascript
db.offices.aggregate([
  {
    $group: {
      _id: null,
      total: { $sum: 1 },
      paises: { $addToSet: "$country" }
    }
  }
])
```

### Resultado

| Campo | Valor |
|--------|--------|
| Total de Escritórios | 5 |
| Países | USA, IRE, ENG, EQU, BRA |

---

# Nível 2: Análise Financeira Básica

## 2.1 — Quantos funcionários trabalham no Departamento de Vendas?

### Comando MongoDB

```javascript
db.employees.countDocuments({
  departmentId: ObjectId("85992103f9b3e0b3b3c1fe71")
})
```

### Resultado

**10 funcionários**

> Pat Ferreira, Payam Kaufling, Sarah Bell, Britney Everett, Alexa Green, Kelly Chung, Jenny Tseng, Michael Hartstein, Jon Deegan, Sundar Ande

---

## 2.2 — Qual é o custo total com salários do Departamento de Vendas?

### Comando MongoDB

```javascript
db.employees.aggregate([
  {
    $match: {
      departmentId: ObjectId("85992103f9b3e0b3b3c1fe71")
    }
  },
  {
    $group: {
      _id: null,
      totalSalarios: { $sum: "$salary" }
    }
  }
])
```

### Resultado

**95.100**

---

## 2.3 — Qual é a média salarial da empresa, excluindo os cargos de CEO, CMO e CFO?

### Comando MongoDB

```javascript
db.employees.aggregate([
  {
    $match: {
      role: { $nin: ["CEO", "CMO", "CFO"] }
    }
  },
  {
    $group: {
      _id: null,
      mediaSalarial: { $avg: "$salary" }
    }
  }
])
```

### Resultado

**9.005,42** *(24 funcionários considerados)*

---

## 2.4 — Qual é a média salarial do Departamento de Tecnologia?

### Comando MongoDB

```javascript
db.employees.aggregate([
  {
    $match: {
      departmentId: ObjectId("85992103f9b3e0b3b3c1fe74")
    }
  },
  {
    $group: {
      _id: null,
      mediaSalarial: { $avg: "$salary" }
    }
  }
])
```

### Resultado

**6.050,00**

---

## 2.5 — Qual departamento possui a maior média salarial?

### Comando MongoDB

```javascript
db.employees.aggregate([
  {
    $group: {
      _id: "$departmentId",
      mediaSalarial: { $avg: "$salary" }
    }
  },
  { $sort: { mediaSalarial: -1 } },
  { $limit: 1 },
  {
    $lookup: {
      from: "departments",
      localField: "_id",
      foreignField: "_id",
      as: "detalhes"
    }
  },
  { $unwind: "$detalhes" }
])
```

### Resultado

| Departamento | Média Salarial |
|-------------|----------------|
| Executive | 71.000,00 |

---

## 2.6 — Qual departamento possui o menor número de funcionários?

### Comando MongoDB

```javascript
db.employees.aggregate([
  {
    $group: {
      _id: "$departmentId",
      totalFuncionarios: { $sum: 1 }
    }
  },
  { $sort: { totalFuncionarios: 1 } },
  { $limit: 1 },
  {
    $lookup: {
      from: "departments",
      localField: "_id",
      foreignField: "_id",
      as: "detalhes"
    }
  },
  { $unwind: "$detalhes" }
])
```

### Resultado

| Departamento | Total de Funcionários |
|-------------|----------------------|
| Marketing | 0 |
| Finance | 0 |
| Data | 0 |

### Observação

Os departamentos de **Marketing**, **Finance** e **Data** possuem **0 funcionários** na base de dados. Eles empatam como os departamentos com menos funcionários. Entre os departamentos com pelo menos um funcionário, o menor é o **Executive** (1 funcionário).

---

# Nível 3: Recursos Humanos

## 3.1 — Quantos funcionários da empresa Momento possuem cônjuges?

### Comando MongoDB

```javascript
db.employees.countDocuments({
  "dependents.conjuge": { $exists: true, $ne: null }
})
```

### Resultado

**6 funcionários**

> Pat Ferreira, David Austin, Valli Stark, Diana Lorentz, Sarah Bell, Normam Osborn

---

## 3.2 — Quantos funcionários possuem filhos registrados?

### Comando MongoDB

```javascript
db.employees.countDocuments({
  "dependents.filhos.0": { $exists: true }
})
```

### Resultado

**6 funcionários**

> Pat Ferreira, Bruce Ernst, Valli Stark, Diana Lorentz, Sarah Bell, Normam Osborn

---

## 3.3 — Qual funcionário foi contratado há mais tempo na empresa?

### Comando MongoDB

```javascript
db.employees.find(
  { hireDate: { $exists: true } }
)
.sort({ hireDate: 1 })
.limit(1)
.project({ firstName: 1, lastName: 1, hireDate: 1, _id: 0 })
```

### Resultado

| Nome | Data de Contratação |
|--------|-------------------|
| Den Raphaely | 1994-12-07 |

---

## 3.4 — Qual funcionário foi contratado há menos tempo na empresa?

### Comando MongoDB

```javascript
db.employees.find(
  { hireDate: { $exists: true } }
)
.sort({ hireDate: -1 })
.limit(1)
.project({ firstName: 1, lastName: 1, hireDate: 1, _id: 0 })
```

### Resultado

| Nome | Data de Contratação |
|--------|-------------------|
| Eduardo Santos / Breno Oliveira / Júlia Lima | 2026-06-03 |

> Os três membros da equipe LATAM foram contratados na mesma data e empatam na posição mais recente.

---

## 3.5 — Liste os 5 funcionários com mais tempo de casa

### Comando MongoDB

```javascript
db.employees.find(
  { hireDate: { $exists: true } }
)
.sort({ hireDate: 1 })
.limit(5)
.project({ firstName: 1, lastName: 1, hireDate: 1, _id: 0 })
```

### Resultado

| Nome | Data de Contratação |
|--------|-------------------|
| Den Raphaely | 1994-12-07 |
| Payam Kaufling | 1995-05-01 |
| Normam Osborn | 1995-05-18 |
| Sarah Bell | 1996-02-04 |
| Elisabeth Braddock | 1996-02-17 |

---

## 3.6 — Quantos funcionários foram contratados na década de 1990?

### Comando MongoDB

```javascript
db.employees.countDocuments({
  hireDate: {
    $gte: "1990-01-01",
    $lte: "1999-12-31"
  }
})
```

### Resultado

**11 funcionários**

> Den Raphaely, Payam Kaufling, Normam Osborn, Sarah Bell, Elisabeth Braddock, Matthew Weiss, Adam Fripp, Shanta Vollman, Britney Everett, Pat Ferreira, Diana Lorentz

---

## 3.7 — Como a média salarial evoluiu ao longo dos anos?

### Comando MongoDB

```javascript
db.employees.aggregate([
  { $match: { hireDate: { $exists: true } } },
  {
    $project: {
      ano: { $substr: ["$hireDate", 0, 4] },
      salary: 1
    }
  },
  {
    $group: {
      _id: "$ano",
      mediaSalarial: { $avg: "$salary" },
      totalFuncionarios: { $sum: 1 }
    }
  },
  { $sort: { _id: 1 } }
])
```

### Resultado

| Ano | Média Salarial | Total de Funcionários |
|------|----------------|----------------------|
| 1994 | 12.500,00 | 1 |
| 1995 | 13.840,00 | 2 |
| 1996 | 30.966,67 | 3 |
| 1997 | 15.275,00 | 4 |
| 1999 | 9.790,00 | 1 |
| 2000 | 4.510,00 | 1 |
| 2005 | 8.400,00 | 1 |
| 2008 | 3.850,00 | 1 |
| 2009 | 2.900,00 | 1 |
| 2026 | 9.166,67 | 3 |

---

# Nível 4: Operações e Escritórios

## 4.1 — Qual é o custo total de suprimentos em cada escritório?

### Comando MongoDB

```javascript
db.offices.aggregate([
  { $unwind: "$supplies" },
  {
    $project: {
      name: 1,
      custo: {
        $multiply: [
          "$supplies.quantity",
          "$supplies.unitPrice"
        ]
      }
    }
  },
  {
    $group: {
      _id: "$name",
      custoTotal: { $sum: "$custo" }
    }
  },
  { $sort: { custoTotal: -1 } }
])
```

### Resultado

| Escritório | Custo Total |
|------------|------------|
| Umbrella Corp | 376.486.500,00 |
| Stark Industries | 189.792,50 |
| Wayne Offices | 151.878,75 |
| Winterfell Offices | 149.142,50 |
| Momento Brasil | 40.000,00 |

---

## 4.2 — Qual escritório possui mais tipos de suprimentos?

### Comando MongoDB

```javascript
db.offices.aggregate([
  {
    $project: {
      name: 1,
      totalTipos: { $size: "$supplies" }
    }
  },
  { $sort: { totalTipos: -1 } },
  { $limit: 1 }
])
```

### Resultado

| Escritório | Total de Tipos |
|------------|---------------|
| Wayne Offices | 6 |

---

## 4.3 — Qual é o suprimento mais caro da empresa?

### Comando MongoDB

```javascript
db.offices.aggregate([
  { $unwind: "$supplies" },
  { $sort: { "supplies.unitPrice": -1 } },
  { $limit: 1 },
  {
    $project: {
      escritorio: "$name",
      produto: "$supplies.product",
      precoUnitario: "$supplies.unitPrice",
      _id: 0
    }
  }
])
```

### Resultado

| Escritório | Produto | Preço Unitário |
|------------|----------|---------------|
| Wayne Offices | Computers | 5.000,00 |
| Winterfell Offices | Livros Didáticos | 5.000,00 |

**Empate:** Computers (Wayne Offices) e Livros Didáticos (Winterfell Offices) — ambos a $5.000,00.

---

## 4.4 — Qual o valor total do inventário da empresa?

### Comando MongoDB

```javascript
db.offices.aggregate([
  { $unwind: "$supplies" },
  {
    $project: {
      valorInventario: {
        $multiply: [
          "$supplies.quantity",
          "$supplies.unitPrice"
        ]
      }
    }
  },
  {
    $group: {
      _id: null,
      valorTotalInventario: { $sum: "$valorInventario" }
    }
  }
])
```

### Resultado

**377.017.313,75**

---

# Nível 5: Produtos e Vendas

## 5.1 — Quais produtos foram vendidos pela Momento?

### Comando MongoDB

```javascript
db.sales.distinct("product")
```

### Resultado

- Superman Uniform
- Fake Batarang
- Web-Shooter
- Ant-Man Helmet
- Ultimate Nullifier
- Lasso of Truth
- Lightsaber (Mace Windu)
- Bolivar Trask Sentinels
- Unstable Molecules Uniform

**Total: 9 produtos únicos**

---

## 5.2 — Qual é o produto mais vendido?

### Comando MongoDB

```javascript
db.sales.aggregate([
  {
    $group: {
      _id: "$product",
      totalQuantidade: { $sum: "$quantity" }
    }
  },
  { $sort: { totalQuantidade: -1 } },
  { $limit: 1 }
])
```

### Resultado

| Produto | Quantidade Total |
|----------|----------------|
| Lasso of Truth | 12 |

---

## 5.3 — Qual é o produto menos vendido?

### Comando MongoDB

```javascript
db.sales.aggregate([
  {
    $group: {
      _id: "$product",
      totalQuantidade: { $sum: "$quantity" }
    }
  },
  { $sort: { totalQuantidade: 1 } },
  { $limit: 1 }
])
```

### Resultado

| Produto | Quantidade Total |
|----------|----------------|
| Superman Uniform | 2 |

---

## 5.4 — Qual produto gerou mais receita para a empresa?

### Comando MongoDB

```javascript
db.sales.aggregate([
  {
    $project: {
      product: 1,
      receita: {
        $multiply: ["$quantity", "$unitPrice"]
      }
    }
  },
  {
    $group: {
      _id: "$product",
      receitaTotal: { $sum: "$receita" }
    }
  },
  { $sort: { receitaTotal: -1 } },
  { $limit: 1 }
])
```

### Resultado

| Produto | Receita Total |
|----------|--------------|
| Lightsaber (Mace Windu) | 7.922,32 |

---

## 5.5 — Qual é o produto mais caro do catálogo?

### Comando MongoDB

```javascript
db.sales.find()
  .sort({ unitPrice: -1 })
  .limit(1)
  .project({ product: 1, unitPrice: 1, _id: 0 })
```

### Resultado

| Produto | Preço Unitário |
|----------|---------------|
| Lightsaber (Mace Windu) | 990,29 |

---

## 5.6 — Qual foi o faturamento total da empresa?

### Comando MongoDB

```javascript
db.sales.aggregate([
  {
    $project: {
      faturamento: {
        $multiply: ["$quantity", "$unitPrice"]
      }
    }
  },
  {
    $group: {
      _id: null,
      faturamentoTotal: { $sum: "$faturamento" }
    }
  }
])
```

### Resultado

**27.076,15**

---

## 5.7 — Quantas vendas foram realizadas em junho de 2023?

### Comando MongoDB

```javascript
db.sales.countDocuments({
  saleDate: {
    $gte: "2023-06-01",
    $lte: "2023-06-30"
  }
})
```

### Resultado

**9 vendas**

---

## 5.8 — Qual vendedor realizou mais vendas?

### Comando MongoDB

```javascript
db.sales.aggregate([
  {
    $group: {
      _id: "$sellerId",
      totalTransacoes: { $sum: 1 }
    }
  },
  { $sort: { totalTransacoes: -1 } },
  { $limit: 1 },
  {
    $lookup: {
      from: "employees",
      localField: "_id",
      foreignField: "_id",
      as: "vendedorInfo"
    }
  },
  { $unwind: "$vendedorInfo" }
])
```

### Resultado

| Vendedor | Total de Transações |
|-----------|-------------------|
| Michael Hartstein | 3 |
| Jon Deegan | 3 |

> Empate — ambos com 3 transações.

---

## 5.9 — Qual vendedor gerou mais receita?

### Comando MongoDB

```javascript
db.sales.aggregate([
  {
    $project: {
      sellerId: 1,
      receita: {
        $multiply: ["$quantity", "$unitPrice"]
      }
    }
  },
  {
    $group: {
      _id: "$sellerId",
      receitaTotal: { $sum: "$receita" }
    }
  },
  { $sort: { receitaTotal: -1 } },
  { $limit: 1 },
  {
    $lookup: {
      from: "employees",
      localField: "_id",
      foreignField: "_id",
      as: "vendedorInfo"
    }
  },
  { $unwind: "$vendedorInfo" }
])
```

### Resultado

| Vendedor | Receita Total |
|-----------|--------------|
| Michael Hartstein | 13.256,17 |

---

# Nível 6: Operações de Atualização

## 6.1 — Adicionar o departamento Inovações

### Comando MongoDB

```javascript
db.departments.insertOne({
  "name": "Innovations",
  "officeId": ObjectId("5f8b3f3f9b3e0b3b3c1e3e3e")
})
```

### Resultado

Inserido com sucesso.

**ID:** `ObjectId("85992103f9b3e0b3b3c1fe77")`

---

## 6.2 — Transferir 2 funcionários para Inovações

Os funcionários transferidos foram **Bruce Ernst** e **Valli Stark**, originalmente no departamento de Tecnologia.

### Comando MongoDB

```javascript
db.employees.updateMany(
  {
    _id: {
      $in: [
        ObjectId("5f8b3f3f9b3e0b3b3c1e3e45"),
        ObjectId("5f8b3f3f9b3e0b3b3c1e3e47")
      ]
    }
  },
  {
    $set: {
      departmentId: ObjectId("85992103f9b3e0b3b3c1fe77")
    }
  }
)
```

### Resultado

**2 documentos atualizados**

---

## 6.3 — Aumento de 10% para Tecnologia

Após a transferência, o departamento de Tecnologia passou a ter 3 funcionários: Alexander Hunold, David Austin e Diana Lorentz.

### Comando MongoDB

```javascript
db.employees.updateMany(
  {
    departmentId: ObjectId("85992103f9b3e0b3b3c1fe74")
  },
  [
    {
      $set: {
        salary: { $multiply: ["$salary", 1.10] }
      }
    }
  ]
)
```

### Resultado

**3 documentos atualizados**

| Funcionário | Salário Antes | Salário Após (+10%) |
|------------|--------------|---------------------|
| Alexander Hunold | 3.500,00 | 3.850,00 |
| David Austin | 4.100,00 | 4.510,00 |
| Diana Lorentz | 8.900,00 | 9.790,00 |

---

## 6.4 — Promoção de Bruce Ernst

### Comando MongoDB

```javascript
db.employees.updateOne(
  { firstName: "Bruce", lastName: "Ernst" },
  {
    $set: { role: "Senior Web Developer" },
    $inc: { salary: 5000 }
  }
)
```

### Resultado

**1 documento atualizado**

> Novo salário: **8.400,00** | Novo cargo: **Senior Web Developer**

---

## 6.5 — Adicionar Headsets ao Wayne Offices

### Comando MongoDB

```javascript
db.offices.updateOne(
  { name: "Wayne Offices" },
  {
    $push: {
      supplies: {
        product: "Headsets",
        quantity: 15,
        unitPrice: 150
      }
    }
  }
)
```

### Resultado

**1 documento atualizado**

> Wayne Offices agora possui **6 tipos** de suprimentos.

---

## 6.6 — Remover funcionários contratados antes de 1990

### Verificação

```javascript
db.employees.find({
  hireDate: { $lt: "1990-01-01" }
})
```

### Resultado da Verificação

Nenhum funcionário encontrado.

### Remoção

```javascript
db.employees.deleteMany({
  hireDate: { $lt: "1990-01-01" }
})
```

### Resultado

**0 documentos removidos**

---

# 🔮 Nível 7: Análise Avançada com Agregações

> **Observação:** Os dados abaixo refletem o estado atual do banco, após todas as operações dos Níveis 6 e 10.

---

## 7.1 — Relatório de Departamentos (Nome, Funcionários, Salário Total e Médio)

### Comando MongoDB

```javascript
db.departments.aggregate([
  {
    $lookup: {
      from: "employees",
      localField: "_id",
      foreignField: "departmentId",
      as: "funcs"
    }
  },
  {
    $project: {
      _id: 0,
      "Departamento": "$name",
      "Número de Funcionários": { $size: "$funcs" },
      "Salário Total": { $sum: "$funcs.salary" },
      "Salário Médio": {
        $round: [
          { $ifNull: [{ $avg: "$funcs.salary" }, 0] },
          2
        ]
      }
    }
  }
])
```

### Resultado

| Departamento | Funcionários | Salário Total | Salário Médio |
|-------------|-------------|---------------|--------------|
| Executive | 1 | 71.000,00 | 71.000,00 |
| Sales | 10 | 95.100,00 | 9.510,00 |
| Marketing | 0 | 0,00 | 0,00 |
| Finance | 0 | 0,00 | 0,00 |
| Technology | 3 | 18.150,00 | 6.050,00 |
| Human Resources | 5 | 64.080,00 | 12.816,00 |
| Data | 0 | 0,00 | 0,00 |
| Innovations | 2 | 11.300,00 | 5.650,00 |
| Operações LATAM | 3 | 27.500,00 | 9.166,67 |

---

## 7.2 — As 3 Funções (Cargos) Mais Comuns

### Comando MongoDB

```javascript
db.employees.aggregate([
  { $group: { _id: "$role", total: { $sum: 1 } } },
  { $sort: { total: -1 } },
  { $limit: 3 },
  {
    $project: {
      _id: 0,
      "Cargo/Função": "$_id",
      "Quantidade": "$total"
    }
  }
])
```

### Resultado

| Cargo/Função | Quantidade |
|-------------|------------|
| Sales Consultant | 8 |
| Web Developer | 3 |
| Stock Manager | 3 |

---

## 7.3 — Funcionários com Salário Acima da Média do seu Departamento

### Comando MongoDB

```javascript
db.employees.aggregate([
  {
    $lookup: {
      from: "employees",
      localField: "departmentId",
      foreignField: "departmentId",
      as: "colegas"
    }
  },
  {
    $project: {
      firstName: 1,
      lastName: 1,
      salary: 1,
      departmentId: 1,
      mediaDept: { $avg: "$colegas.salary" }
    }
  },
  {
    $match: {
      $expr: { $gt: ["$salary", "$mediaDept"] }
    }
  },
  {
    $lookup: {
      from: "departments",
      localField: "departmentId",
      foreignField: "_id",
      as: "deptInfo"
    }
  },
  {
    $unwind: {
      path: "$deptInfo",
      preserveNullAndEmptyArrays: true
    }
  },
  {
    $project: {
      _id: 0,
      "Funcionário": {
        $concat: [
          "$firstName", " ",
          { $ifNull: ["$lastName", ""] }
        ]
      },
      "Departamento": { $ifNull: ["$deptInfo.name", "Sem Dept"] },
      "Salário": "$salary",
      "Média do Dept": { $round: ["$mediaDept", 2] }
    }
  }
])
```

### Resultado

| Funcionário | Departamento | Salário | Média do Departamento |
|------------|-------------|----------|----------------------|
| Pat Ferreira | Sales | 34.000,00 | 9.510,00 |
| Diana Lorentz | Technology | 9.790,00 | 6.050,00 |
| Bruce Ernst | Innovations | 8.400,00 | 5.650,00 |
| Matthew Weiss | Human Resources | 14.000,00 | 12.816,00 |
| Payam Kaufling | Sales | 9.600,00 | 9.510,00 |
| Normam Osborn | Human Resources | 18.080,00 | 12.816,00 |
| Júlia Lima | Operações LATAM | 9.500,00 | 9.166,67 |

---

## 7.4 — Taxa de Crescimento da Empresa por Ano (Contratações)

### Comando MongoDB

```javascript
db.employees.aggregate([
  { $match: { hireDate: { $exists: true } } },
  {
    $project: {
      ano: { $substr: ["$hireDate", 0, 4] }
    }
  },
  {
    $group: {
      _id: "$ano",
      contratados: { $sum: 1 }
    }
  },
  { $sort: { _id: 1 } },
  {
    $project: {
      _id: 0,
      "Ano de Contratação": "$_id",
      "Qtd Contratados": "$contratados"
    }
  }
])
```

### Resultado

| Ano | Quantidade Contratada |
|------|----------------------|
| 1994 | 1 |
| 1995 | 2 |
| 1996 | 3 |
| 1997 | 4 |
| 1999 | 1 |
| 2000 | 1 |
| 2005 | 1 |
| 2008 | 1 |
| 2009 | 1 |
| 2026 | 3 |

---

## 7.5 — Ranking dos Vendedores (Número de Vendas Realizadas e Receita Total)

### Comando MongoDB

```javascript
db.sales.aggregate([
  {
    $group: {
      _id: "$sellerId",
      totalVendas: { $sum: 1 },
      receita: {
        $sum: { $multiply: ["$quantity", "$unitPrice"] }
      }
    }
  },
  {
    $lookup: {
      from: "employees",
      localField: "_id",
      foreignField: "_id",
      as: "vendedor"
    }
  },
  {
    $unwind: {
      path: "$vendedor",
      preserveNullAndEmptyArrays: true
    }
  },
  { $sort: { receita: -1 } },
  {
    $project: {
      _id: 0,
      "Vendedor": {
        $concat: [
          "$vendedor.firstName", " ",
          { $ifNull: ["$vendedor.lastName", ""] }
        ]
      },
      "Vendas Realizadas": "$totalVendas",
      "Receita Total Gerada": { $round: ["$receita", 2] }
    }
  }
])
```

### Resultado

| Vendedor | Vendas Realizadas | Receita Total |
|-----------|------------------|---------------|
| Michael Hartstein | 3 | 13.256,17 |
| Jon Deegan | 3 | 4.459,11 |
| Jenny Tseng | 2 | 4.051,08 |
| Sundar Ande | 2 | 3.107,94 |
| Normam Osborn | 1 | 711,57 |
| Alexa Green | 1 | 478,58 |
| Kelly Chung | 1 | 300,13 |
| Sem vendedor identificado | 1 | 711,57 |

---

## 7.6 — Produtos que Foram Vendidos Apenas por um Vendedor

### Comando MongoDB

```javascript
db.sales.aggregate([
  {
    $group: {
      _id: "$product",
      vendedores: { $addToSet: "$sellerId" }
    }
  },
  {
    $match: {
      $expr: { $eq: [{ $size: "$vendedores" }, 1] }
    }
  },
  {
    $project: {
      _id: 0,
      "Produto Exclusivo de 1 Vendedor": "$_id"
    }
  }
])
```

### Resultado

| Produto | Vendedor Exclusivo |
|---------|-------------------|
| Fake Batarang | Alexa Green |
| Lightsaber (Mace Windu) | Michael Hartstein |
| Bolivar Trask Sentinels | Jon Deegan |
| Unstable Molecules Uniform | Michael Hartstein |

> **Observação:** Superman Uniform, Web-Shooter, Ant-Man Helmet, Ultimate Nullifier e Lasso of Truth foram vendidos por mais de um vendedor (ou sem vendedor identificado), portanto não aparecem nesta lista.

---

# ⚡ Nível 8: Desafios e Otimização

---

## 8.1 — Funcionários de Vendas com Dependentes (Cônjuge ou Filhos)

### Maneira 1 (find nativo)

```javascript
db.employees.find({
  "departmentId": ObjectId("85992103f9b3e0b3b3c1fe71"),
  $or: [
    { "dependents.conjuge": { $exists: true, $ne: null } },
    { "dependents.filhos.0": { $exists: true } }
  ]
})
```

### Maneira 2 (Aggregation)

```javascript
db.employees.aggregate([
  {
    $match: {
      "departmentId": ObjectId("85992103f9b3e0b3b3c1fe71"),
      $or: [
        { "dependents.conjuge": { $exists: true } },
        { "dependents.filhos.0": { $exists: true } }
      ]
    }
  }
])
```

### Resultado

**2 funcionários encontrados**

| Funcionário | Dependentes |
|------------|-------------|
| Pat Ferreira | Cônjuge: Ana Ferreira / Filhos: João e Maria |
| Sarah Bell | Cônjuge: J. Bell / Filho: John |

---

## 8.2 — Inconsistência de Escritório

### Comando MongoDB

```javascript
db.employees.aggregate([
  {
    $lookup: {
      from: "departments",
      localField: "departmentId",
      foreignField: "_id",
      as: "dept"
    }
  },
  { $unwind: "$dept" },
  {
    $match: {
      $expr: {
        $ne: ["$officeId", "$dept.officeId"]
      }
    }
  },
  {
    $project: {
      _id: 0,
      "Funcionário": "$firstName",
      "Inconsistência": "Campo 'officeId' ausente ou nulo no documento do funcionário."
    }
  }
])
```

### Resultado

Retorna todos os funcionários originais, pois o schema de `employees` não possui um campo `officeId` diretamente no documento do funcionário. O vínculo com o escritório ocorre de forma indireta: `employee → departmentId → department → officeId`.

---

## 8.3 — Relatório Completo de Escritório (Departamentos, Funcionários, Salários e Suprimentos)

### Comando MongoDB

```javascript
db.offices.aggregate([
  {
    $lookup: {
      from: "departments",
      localField: "_id",
      foreignField: "officeId",
      as: "depts"
    }
  },
  {
    $lookup: {
      from: "employees",
      localField: "depts._id",
      foreignField: "departmentId",
      as: "funcs"
    }
  },
  {
    $project: {
      _id: 0,
      "Nome do escritório": "$name",
      "País": "$country",
      "Número de departamentos": { $size: "$depts" },
      "Número total de funcionários": { $size: "$funcs" },
      "Custo total com salários": { $sum: "$funcs.salary" },
      "Custo total com suprimentos": {
        $sum: {
          $map: {
            input: { $ifNull: ["$supplies", []] },
            as: "item",
            in: { $multiply: ["$$item.quantity", "$$item.unitPrice"] }
          }
        }
      }
    }
  }
])
```

### Resultado

| Escritório | País | Departamentos | Funcionários | Salários | Suprimentos |
|------------|------|--------------|--------------|-----------|-------------|
| Wayne Offices | USA | 5 | 11 | 164.530,00 | 151.878,75 |
| Winterfell Offices | IRE | 1 | 10 | 95.100,00 | 149.142,50 |
| Stark Industries | ENG | 2 | 0 | 0,00 | 189.792,50 |
| Umbrella Corp | EQU | 0 | 0 | 0,00 | 376.486.500,00 |
| Momento Brasil | BRA | 1 | 3 | 27.500,00 | 40.000,00 |

---

## 8.4 — Departamento Mais Equilibrado (Menor diferença salarial)

### Comando MongoDB

```javascript
db.employees.aggregate([
  {
    $match: {
      salary: { $ne: null },
      departmentId: { $type: "objectId" }
    }
  },
  {
    $group: {
      _id: "$departmentId",
      maxSal: { $max: "$salary" },
      minSal: { $min: "$salary" }
    }
  },
  {
    $project: {
      diferenca: { $subtract: ["$maxSal", "$minSal"] }
    }
  },
  { $sort: { diferenca: 1 } },
  { $limit: 1 },
  {
    $lookup: {
      from: "departments",
      localField: "_id",
      foreignField: "_id",
      as: "d"
    }
  },
  { $unwind: "$d" },
  {
    $project: {
      _id: 0,
      "Departamento Mais Equilibrado": "$d.name",
      "Diferença Salarial": "$diferenca"
    }
  }
])
```

### Resultado

| Departamento | Diferença Salarial |
|-------------|-------------------|
| Operações LATAM | 500,00 |

**Maior salário do dept.:** 9.500,00 (Júlia Lima — UI/UX Designer)  
**Menor salário do dept.:** 9.000,00 (Eduardo Santos / Breno Oliveira — Developers)

---

## 8.5 — Pesquisa de Texto ("Uniform")

### Comando MongoDB

```javascript
db.sales.find({
  product: { $regex: /Uniform/i }
})
```

### Resultado

**2 documentos encontrados**

- Superman Uniform
- Unstable Molecules Uniform

---

## 8.6 — Vendas Realizadas no 2º Trimestre de 2023

### Comando MongoDB

```javascript
db.sales.find({
  saleDate: {
    $gte: "2023-04-01",
    $lte: "2023-06-30"
  }
})
```

### Resultado

**9 vendas encontradas** *(todas as transações da coleção ocorreram em junho de 2023, não há registros de abril ou maio)*

---

# 🥷 Nível 9: Desafios Ninja

---

## 9.1 — Filtro de Salário entre $6.000 e $10.000 (3 Formas)

### Forma 1

```javascript
db.employees.find({
  salary: { $gte: 6000, $lte: 10000 }
})
```

### Forma 2

```javascript
db.employees.find({
  $and: [
    { salary: { $gte: 6000 } },
    { salary: { $lte: 10000 } }
  ]
})
```

### Forma 3

```javascript
db.employees.aggregate([
  {
    $match: {
      salary: { $gte: 6000, $lte: 10000 }
    }
  }
])
```

### Resultado

**16 funcionários encontrados**

| Funcionário | Salário |
|------------|---------|
| Bruce Ernst | 8.400,00 |
| Diana Lorentz | 9.790,00 |
| Adam Fripp | 9.800,00 |
| Payam Kaufling | 9.600,00 |
| Shanta Vollman | 9.700,00 |
| Sarah Bell | 7.900,00 |
| Britney Everett | 7.600,00 |
| Alexa Green | 6.000,00 |
| Kelly Chung | 6.000,00 |
| Jenny Tseng | 6.000,00 |
| Michael Hartstein | 6.000,00 |
| Jon Deegan | 6.000,00 |
| Sundar Ande | 6.000,00 |
| Eduardo Santos | 9.000,00 |
| Breno Oliveira | 9.000,00 |
| Júlia Lima | 9.500,00 |

---

## 9.2 — Otimização de Consulta

### Resultado

A **Consulta A** (com `$match` antes de `$project`) é a mais eficiente porque delega a filtragem diretamente à engine indexada do MongoDB, reduzindo o volume de documentos processados antes de qualquer transformação.

---

## 9.3 — Funcionários Sem E-mail OU Sem Telefone

### Comando MongoDB

```javascript
db.employees.find({
  $or: [
    { email: { $exists: false } },
    { email: null },
    { phone: { $exists: false } },
    { phone: null }
  ]
})
```

### Resultado

| Funcionário | E-mail | Telefone |
|------------|--------|---------|
| Alexa Green | ✗ | ✓ |
| Kelly Chung | ✗ | ✓ |
| Jenny Tseng | ✗ | ✓ |
| Michael Hartstein | ✗ | ✓ |
| Jon Deegan | ✗ | ✓ |
| Sundar Ande | ✗ | ✓ |
| Cauan Rodrigues Morária | ✗ | ✗ |
| Eduardo Santos | ✗ | ✗ |
| Breno Oliveira | ✗ | ✗ |
| Júlia Lima | ✗ | ✗ |

> Os 6 Sales Consultants `commissioned: true` não possuem e-mail cadastrado. Os 4 funcionários inseridos manualmente (Cauan + equipe LATAM) não possuem e-mail nem telefone.

---

## 9.4 — Funcionários Solitários (Único no cargo dentro do departamento)

### Resultado

| Funcionário | Cargo | Departamento |
|------------|-------|-------------|
| Elisabeth Braddock | CEO | Executive |
| Pat Ferreira | Sales Representative for Latin America | Sales |
| Payam Kaufling | Vendas | Sales |
| Diana Lorentz | Web Developer Senior | Technology |
| Bruce Ernst | Senior Web Developer | Innovations |
| Valli Stark | Web Developer | Innovations |
| Den Raphaely | Gerente de Recursos | Human Resources |
| Normam Osborn | Gerente de Recursos Químicos | Human Resources |
| Cauan Rodrigues Morária | Owner | — |
| Júlia Lima | UI/UX Designer | Operações LATAM |

---

## 9.5 — Pipeline Complexo por País

### Resultado

| País | Departamentos | Funcionários | Custo Global |
|------|--------------|--------------|--------------|
| USA | 5 | 11 | 316.408,75 |
| IRE | 1 | 10 | 244.242,50 |
| ENG | 2 | 0 | 189.792,50 |
| EQU | 0 | 0 | 376.486.500,00 |
| BRA | 1 | 3 | 67.500,00 |

> Custo Global = salários + suprimentos do escritório.

---

# 🏢 Nível 10: Casos Práticos e Simulações

> **Observação:** A partir deste ponto inicia-se a injeção e alteração cronológica dos dados da base original.

---

## Cenário 1: Expansão Internacional

### 10.1 — Criar o Escritório "Momento Brasil"

```javascript
db.offices.insertOne({
  "_id": ObjectId("6491a999f9b3e0b3b3c1f001"),
  "name": "Momento Brasil",
  "city": "São Paulo",
  "country": "BRA",
  "supplies": [
    {
      "product": "Notebooks",
      "quantity": 10,
      "unitPrice": 4000
    }
  ]
})
```

---

### 10.2 — Criar o Departamento "Operações LATAM"

```javascript
db.departments.insertOne({
  "_id": ObjectId("6491a999f9b3e0b3b3c1f002"),
  "name": "Operações LATAM",
  "officeId": ObjectId("6491a999f9b3e0b3b3c1f001")
})
```

---

### 10.3 — Inserir a Nova Equipe

```javascript
db.employees.insertMany([
  {
    "firstName": "Eduardo",
    "lastName": "Santos",
    "role": "Developer",
    "salary": 9000,
    "departmentId": ObjectId("6491a999f9b3e0b3b3c1f002"),
    "hireDate": "2026-06-03"
  },
  {
    "firstName": "Breno",
    "lastName": "Oliveira",
    "role": "Developer",
    "salary": 9000,
    "departmentId": ObjectId("6491a999f9b3e0b3b3c1f002"),
    "hireDate": "2026-06-03"
  },
  {
    "firstName": "Júlia",
    "lastName": "Lima",
    "role": "UI/UX Designer",
    "salary": 9500,
    "departmentId": ObjectId("6491a999f9b3e0b3b3c1f002"),
    "hireDate": "2026-06-03"
  }
])
```

---

## Cenário 2: Crise Financeira

### 10.4 — Três escritórios mais caros (suprimentos)

| Posição | Escritório | Valor |
|----------|------------|--------|
| 1º | Umbrella Corp | 376.486.500,00 |
| 2º | Stark Industries | 189.792,50 |
| 3º | Wayne Offices | 151.878,75 |

---

### 10.5 — Suprimentos com Quantidade ≥ 50

| Escritório | Produto | Quantidade |
|------------|---------|-----------|
| Wayne Offices | A4 Paper Kit | 50 |
| Wayne Offices | Post-its | 50 |
| Winterfell Offices | Papel | 50 |
| Winterfell Offices | Post-its | 50 |
| Stark Industries | Iron Gloves (unit) | 143 |
| Stark Industries | A4 Paper | 50 |
| Stark Industries | Post-its | 50 |
| Umbrella Corp | Sulphite Sheets | 500.000 |
| Umbrella Corp | Post-its | 50 |

---

### 10.6 — Economia Projetada de 20%

Aplicando 20% de corte sobre o custo total de suprimentos (**377.017.313,75**):

**75.403.462,75** de economia projetada.

---

## Cenário 3: Auditoria de Vendas

### 10.7 — Vendas Sem Vendedor

**1 documento localizado**

- **Web-Shooter** — 3 unidades — valor da transação: **711,57**

---

### 10.8 — Vendedor Fora do Cargo de Vendas

**Normam Osborn** está vinculado a uma venda de Web-Shooter, porém o RH o classifica como:

**Gerente de Recursos Químicos** — Departamento: **Human Resources**

Essa é uma irregularidade de auditoria: funcionário de RH registrado como vendedor.

---

### 10.9 — Variação Cambial/Preço

**0% de variação**

Os valores unitários são idênticos em todas as transações do mesmo produto. Não há registros de alteração de preço ao longo do período.

---

## Cenário 4: Dashboard Consolidado (Métricas Finais)

### Comando MongoDB

```javascript
db.employees.aggregate([
  {
    $facet: {
      "Kpis_RH": [
        {
          $group: {
            _id: null,
            totalFuncs: { $sum: 1 },
            custoSalarios: { $sum: "$salary" }
          }
        }
      ],
      "Kpis_Depts": [
        { $group: { _id: "$departmentId" } },
        { $group: { _id: null, totalDepts: { $sum: 1 } } }
      ],
      "Kpis_Vendas": [
        {
          $lookup: {
            from: "sales",
            pipeline: [
              {
                $group: {
                  _id: "$product",
                  receitaProd: {
                    $sum: { $multiply: ["$quantity", "$unitPrice"] }
                  },
                  qtdProd: { $sum: "$quantity" }
                }
              },
              { $sort: { qtdProd: -1 } }
            ],
            as: "vendasDetalhadas"
          }
        },
        { $unwind: "$vendasDetalhadas" },
        {
          $group: {
            _id: null,
            receitaGlobal: { $sum: "$vendasDetalhadas.receitaProd" },
            maisVendido: { $first: "$vendasDetalhadas._id" }
          }
        }
      ]
    }
  },
  {
    $project: {
      _id: 0,
      "Total de Funcionários": { $arrayElemAt: ["$Kpis_RH.totalFuncs", 0] },
      "Custo Total com Salários": { $arrayElemAt: ["$Kpis_RH.custoSalarios", 0] },
      "Número de Departamentos": { $arrayElemAt: ["$Kpis_Depts.totalDepts", 0] },
      "Receita Total de Vendas": {
        $round: [{ $arrayElemAt: ["$Kpis_Vendas.receitaGlobal", 0] }, 2]
      },
      "Produto Mais Vendido": { $arrayElemAt: ["$Kpis_Vendas.maisVendido", 0] }
    }
  }
])
```

### Resultado

| Indicador | Valor |
|------------|--------|
| Total de Funcionários | 25 |
| Custo Total com Salários | 287.130,00 |
| Número de Departamentos | 9 |
| Receita Total de Vendas | 27.076,15 |
| Produto Mais Vendido (qtd) | Lasso of Truth |

### Observação

- 22 funcionários da base original + 3 colaboradores da equipe LATAM.
- O custo do owner (Cauan) é 0, portanto não impacta a folha salarial.
- Acréscimo de **27.500,00** mensais na folha com a expansão LATAM.
- Estrutura consolidada após todas as simulações executadas.
