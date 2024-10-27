# Momento 

Contém a base de indicados da empresa Momento para treinar consultas complexas no MongoDB.

Vamos fazer algumas perguntas para brincar de análise exploratória de dados com MongoDB.

* Quantos funcionarios da empresa Momento trabalham no departamento de vendas?

R: Na empresa Momento tem 8 funcionários no departamento de vendas.

<pre>db.funcionarios.countDocuments({cargo:"Consultor de Vendas"});</pre>

<br>

* Inclua suas próprias informações no departamento de Tecnologia da empresa.

R: <pre>db.funcionarios.insertOne({"_id": ObjectId, "nome": "Gustavo Correia", "telefone": "11942886143", "cargo": "Desenvolvedor Mobile", "salario": 8000, "comissao": true, "departamento": "Tecnologia"});</pre>

<br>

* Agora diga, quantos funcionários temos ao total na empresa?

R: Agora a Empresa Momento possui 24 funcionários.

<pre>db.funcionarios.countDocuments()</pre>

<br>

* E quanto ao Departamento de Tecnologia?

R: Agora a Empresa Momento possui 2 funcionários no departamento de Tecnologia.

<pre>db.departamentos.countDocuments({nome: "Tecnologia"});</pre>

<br>

* Quanto o departamento de Vendas gasta em salários?

R: O departamento de Vendas gasta R$95100 em salários.

<pre>
    db.funcionarios.aggregate([
    {
        $match: { "cargo": /vendas/i }
    },
    {
        $group: {
            _id: null,
            gasto_em_salario: { $sum: "$salario" }
        }
    }
]) </pre>

<br>

* Um novo departamento foi criado. O departamento de Inovações. 
Ele será locado no Brasil. Por favor, adicione-o no banco de dados da empresa colocando quaisquer informações que você achar relevantes.

R: <pre>db.departamentos.insertOne({
    "_id": ObjectId("85992103f9b3e0b3b3c1fe74"),
    "nome": "Inovações",
    "escritorio": ObjectId("5f8b3f3f9b3e0b3b3c1e3e3e")});</pre>


<br>

* O departamento de Inovações está sem funcionários. Inclua alguns colegas de turma nesse departamento.  

R: <pre>db.funcionarios.insertMany([
    {
        "nome": "Glenda Alves",
        "telefone": "(11) 98745-6343",
        "email": "glendaalvess133@gmail.com",
        "dataAdmissao": "2019-06-21",
        "cargo": "Analista de Banco de Dados",
        "salario": 23000,
        "departamento": ObjectId('66f1c0efd15d5494e3f31947'),
    },
    {
        "nome": "Matheus Oliveira",
        "telefone": "(11) 94354-1015",
        "email": "matheusoliveira10@gmail.com",
        "dataAdmissao": "2015-03-06",
        "cargo": "Desenvolvedor Back-End",
        "salario": 10000,
        "departamento": ObjectId('66f1c0efd15d5494e3f31947')
    },
    {
        "nome": "Murilo Coelho",
        "telefone": "(11) 99653-5332",
        "email": "murilocoelho125@gmail.com",
        "dataAdmissao": "2012-02-10",
        "cargo": "Product Owner",
        "salario": 15000,
        "departamento": ObjectId('66f1c0efd15d5494e3f31947'),
    },
    {
        "nome": "Gustavo Rocha Cunha",
        "telefone": "(11) 93085-3174",
        "email": "gucunha@gmail.com",
        "dataAdmissao": "2022-08-13",
        "cargo": "Desenvolvedor Full Stack",
        "salario": 15000,
        "departamento": ObjectId('66f1c0efd15d5494e3f31947'),
    },
    {
        "nome": "Hudson Souza",
        "telefone":  "(11) 98305-7222",
        "email": "hudsonpalestra@gmail.com",
        "dataAdmissao": "2019-05-18",
        "cargo": "Desenvolvedor Front End",
        "salario": 15000,
        "departamento": ObjectId('66f1c0efd15d5494e3f31947'),
    },
    {
        "nome": "Kawan Barbosa Turchiai",
        "telefone": "(11) 91116-1868",
        "email": "kawant@gmail.com",
        "dataAdmissao": "2020-03-20",
        "cargo": "Desenvolvedor Front End",
        "salario": 13000,
        "departamento": ObjectId('66f1c0efd15d5494e3f31947')
    }, 
    {
        "nome": "Celio Amorim",
        "telefone": "(11) 92226-1868",
        "email": "celioa@gmail.com",
        "dataAdmissao": "2020-03-20",
        "cargo": "Desenvolvedor Front End",
        "salario": 13000,
        "departamento": ObjectId('66f1c0efd15d5494e3f31947')
    }
]);</pre>

<br>

* Quantos funcionarios a empresa Momento tem agora?

R: A empresa Momento possui 31 funcionários agora.

<pre>db.funcionarios.countDocuments()</pre>

<br>

* Quantos funcionários da empresa Momento possuem conjuges?

R: Na empresa Momento apenas 7 funcionários possuem conjuges.

<pre>db.funcionarios.aggregate([
    { $match: { "dependentes.conjuge": { $exists: true } } },
    { $count: "totalFuncionariosComConjuge" }
])</pre>

<br>

* Qual a média salarial dos funcionários da empresa Momento, excluindo-se o CEO?

R: A média salarial dos funcionários da empresa Momento, tirando o CEO é de R$10856.

<pre>
    db.funcionarios.aggregate([
    {
        $match: {
            cargo: { $ne: "CEO" }
        }
    },
    {
        $group:{
            _id: null,
            mediaSalarial: { $avg: "$salario" }
        }
    } 
])
</pre>

<br>

* Qual a média salarial do departamento de tecnologia? 

R: A média salarial do departamento de tecnologia é 8000.

<pre>
    db.funcionarios.aggregate([
    {
        $match: { "departamento": "Tecnologia" }
    },
    {
        $group: {
            _id: null,
            a_media_salarial: { $avg: "$salario" }
        }
    }
])
</pre>

<br>

* Qual o departamento com a maior média salarial?

R: O departamento com a maior média salarial é o "Executivo", a média do salário desse departamento é de R$71000.

<pre>db.funcionarios.aggregate([
    {
        $group: {
            _id: "$departamento",
            mediaSalarial: { $avg: "$salario" }
        }
    },
    {
        $lookup: {
            from: "departamentos",
            localField: "_id",            
            foreignField: "_id",
            as: "informacoesDepartamento"
        }
    },
    {
        $sort: { mediaSalarial: -1 }
    },
    {
        $limit: 1
    },
    {
        $project: {
            _id: 0,
            mediaSalarial: 1,
            "informacoesDepartamento.nome": 1
        }
    }
])</pre>

<br>

* Qual o departamento com o menor número de funcionários?

R: É o departamento de Executivo, com apenas 1 funcionário.

<pre>db.funcionarios.aggregate([
    {
        $group: {
            _id: "$departamento",
            totalFuncionarios: { $sum: 1 }
        }
    },
    {
        $lookup: {
            from: "departamentos",
            localField: "_id",
            foreignField: "_id",
            as: "infoDepartamento"
        }
    },
    {
        $sort: { totalFuncionarios: 1 }
    },
    {
        $limit: 1
    },
    {
        $project: {
            _id: 0,
            totalFuncionarios: 1,
            nomeDepartamento: { $arrayElemAt: ["$infoDepartamento.nome", 0] }
        }
    }
])</pre>

<br>

* Pensando na relação quantidade e valor unitario, qual o produto mais valioso da empresa?

R: O produto mais valioso da empresa Momento são os computadores.

<pre>db.escritorios.aggregate([
    {
        "$unwind": "$suprimentos"
    },
    {
        "$lookup": {
            "from": "departamentos",
            "localField": "departamento",
            "foreignField": "_id",
            "as": "infoDepartamento"
        }
    },
    {
        "$group": {
            "_id": "$suprimentos",
            "quantidade": { "$sum": 1 },
            "valorUnitario": { "$avg": "$infoDepartamento.preco" }
        }
    },
    {
        "$sort": {
                "quantidade": -1,
                "valorUnitario": -1
            }
    }
])</pre>

<br>

* Qual o produto mais vendido da empresa?

R: O produto mais vendido da Empresa Momento é o Laço da Verdade.

<pre>
    db.vendas.aggregate([
    {
        "$group": {
            "_id": "$produto",
            "count": { $sum: "$quantidade" }
        }
    },
    {
        "$sort": { "count": -1 }
    },
    {
        "$limit": 1
    }
])
</pre>

<br>

* Qual o produto menos vendido da empresa?

R: O produto menos vendido da empresa Momento é o Uniforme do Superman.

<pre>
    db.vendas.aggregate([
    {
        "$group": {
            "_id": "$produto",
            "totalQuantidade": { "$sum": "$quantidade" },
            "totalVendas": { "$sum": { "$multiply": ["$quantidade", "$precoUnitario"] } }
        }
    },
    {
        "$sort": { "totalQuantidade": 1 }
    },
    {
        "$limit": 1
    },
    {
        "$project": {
            "_id": 0,
            "produto": "$_id",
            "totalQuantidade": 1,
            "totalVendas": 1
        }
    }
]);
</pre>

<br>
