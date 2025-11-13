# Usuario_model
from sqlalchemy import Column, Integer, String 
from passlib.hash import pbkdf2_sha256 as sha256 

class Usuario():
    
    #construtor - quando quero usar os atributos para novos valores
    def __init__(self, nome, email, senha):
        self.__nome = nome
        self.__email = email
        self.__email = senha

#GET
    @property
    def nome(self):
        return self.__nome

    @nome.setter
    def nome(self, nome):
        self.__nome = nome
    
    @property
    def email(self):
        return self.__email
    
    @email.setter
    def email(self, email):
        self._email = email

    
   
    __tablename__ = 'USUARIO'
    
    id = Column(Integer, primary_key =True, autoincrement =True)
    nome = Column(String(120), nullable=False)
    email = Column(String(120), nullable=False, unique =True)
    senha = Column(String(255), nullable=False)
        
    def gen_senha(sef, senha):
        return sha256.hash(senha)
    
    def verifica_senha(self, senha):
        return sha256.verify(self.senha, senha)

#usuario_service

from src.models.usuario_model import Usuario
from sqlalhemy.orm import Session

session = Session()

def criar_usuario(usuario):
    usuario_db = Usuario(nome=usuario.nome, email=usuario.email, senha=usuario.senha)
    usuario_db.senha = usuario.gen_senha(usuario.senha)

    session.add(usuario_db)
    session.commit()
    return usuario_db

def listar_usuario_email(email):
    usuario_db = session.query(Usuario).filter(Usuario.email == email).first()
    return usuario_db

def listar_usuarios_id(id):
    usuario_db = session.query(Usuario).filter(Usuario.id == id).first()
    return usuario_db

def listar_usuarios():
    usuarios_db = session.query(Usuario).all()
    return usuarios_db

def excluir_usuario(usuario):
    usuario_db = session.query(Usuario).filter(Usuario.id == usuario.id).first()
    if usuario_db:
        session.delete(usuario_db)
        session.commit()
        return True
    return False
def editar_usuario(usuario):
    usuario_db = session.query(Usuario).filter(Usuario.id == usuario.id).first()
    if usuario_db:
        usuario_db.nome = usuario.nome
        usuario_db.email = usuario.email
        session.commit()
        return usuario_db
    return None

#usuario_connection
from sqlalchemy import create_engine
from sqlalchemy.orm import declarative_base


SQLALCHEMY_BATABASE_URI = 'sqlite:///controle_usuario.db'

try:
    engine = create_engine(SQLALCHEMY_BATABASE_URI)
    conntion = engine.connect()
    print('Banco conectado com sucesso!')
except Exception as e:
    print('Erro ao conectar com o Banco de Dados')
