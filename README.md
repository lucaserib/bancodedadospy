from sqlalchemy import create_engine, Column, Integer, String
from sqlalchemy.orm import sessionmaker
from sqlalchemy.ext.declarative import declarative_base
import pymongo

# Parte 1: Banco de Dados Relacional com SQLAlchemy

# Conecte-se ao banco de dados SQLite (ou configure o banco de dados da maneira apropriada).
engine = create_engine('sqlite:///mydatabase.db')
Base = declarative_base()

class Cliente(Base):
    __tablename__ = 'cliente'

    id = Column(Integer, primary_key=True)
    nome = Column(String)
    email = Column(String)

    def __init__(self, nome, email):
        self.nome = nome
        self.email = email

class Conta(Base):
    __tablename__ = 'conta'

    id = Column(Integer, primary_key=True)
    numero = Column(String)
    saldo = Column(Integer)
    cliente_id = Column(Integer)

    def __init__(self, numero, saldo, cliente_id):
        self.numero = numero
        self.saldo = saldo
        self.cliente_id = cliente_id

Base.metadata.create_all(engine)

Session = sessionmaker(bind=engine)
session = Session()

# Inserir dados para a Parte 1
novo_cliente = Cliente(nome="João da Silva", email="joao@example.com")
session.add(novo_cliente)
session.commit()

nova_conta = Conta(numero="12345", saldo=1000, cliente_id=novo_cliente.id)
session.add(nova_conta)
session.commit()

# Parte 2: Banco de Dados NoSQL com PyMongo

# Conecte-se ao MongoDB Atlas (substitua com sua própria URL de conexão).
client = pymongo.MongoClient("mongodb+srv://<username>:<password>@cluster.mongodb.net/test?retryWrites=true&w=majority")
db = client["banco_de_dados"]
collection = db["bank"]

# Inserir documentos para a Parte 2
cliente1 = {
    "nome": "João da Silva",
    "email": "joao@example.com",
    "contas": [
        {
            "numero": "12345",
            "saldo": 1000
        }
    ]
}

cliente2 = {
    "nome": "Maria Souza",
    "email": "maria@example.com",
    "contas": [
        {
            "numero": "54321",
            "saldo": 2000
        }
    ]
}

collection.insert_one(cliente1)
collection.insert_one(cliente2)

# Recuperar informações com base em critérios da Parte 2
saldo_alvo = 1000
clientes_com_saldo_alvo = collection.find({"contas.saldo": saldo_alvo})

for cliente in clientes_com_saldo_alvo:
    print(f"Nome: {cliente['nome']}")
    print(f"Email: {cliente['email']}")
    for conta in cliente['contas']:
        if conta['saldo'] == saldo_alvo:
            print(f"Conta Número: {conta['numero']}")
            print(f"Saldo: {conta['saldo']}")
            print("\n")
