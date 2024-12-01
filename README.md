## Códigos do MySQL (lembrando que no Healthtrack tem que arrumar o usuário em: .env)
## .env: DB_HOST=localhost
##         DB_USER=(troque pelo seu user no Mysql)
##         DB_PASSWORD=(aqui tbm)
##         DB_NAME=healthtrack


-- Criação do banco de dados
CREATE DATABASE healthtrack;

-- Usar o banco de dados
USE healthtrack;

CREATE TABLE monitoramento (
    id INT AUTO_INCREMENT PRIMARY KEY,
    usuario_id INT NOT NULL,
    peso DECIMAL(5,2) NOT NULL,
    altura DECIMAL(5,2) NOT NULL,
    imc DECIMAL(5,2) NOT NULL,
    classificacao VARCHAR(50) NOT NULL,
    meta_peso DECIMAL(5,2) DEFAULT NULL,
    data_criacao TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (usuario_id) REFERENCES usuarios(id)
);

-- Tabela de Usuários
CREATE TABLE usuarios (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(255) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    senha VARCHAR(255) NOT NULL,
    peso DECIMAL(5,2) DEFAULT NULL,
    altura DECIMAL(5,2) DEFAULT NULL,
    idade INT DEFAULT NULL,
    data_criacao TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    ativo BOOLEAN DEFAULT TRUE
);
CREATE TABLE metas_saude (
  id INT AUTO_INCREMENT PRIMARY KEY,
  usuario_id INT,
  meta VARCHAR(255),
  valor_meta DECIMAL(5,2),
  data_inicio DATE,
  data_fim DATE,
  FOREIGN KEY (usuario_id) REFERENCES usuarios(id)
);


-- Tabela para registrar o histórico do IMC
CREATE TABLE historico_imc (
  id INT AUTO_INCREMENT PRIMARY KEY,
  usuario_id INT,
  peso DECIMAL(5,2),
  altura DECIMAL(5,2),
  imc DECIMAL(5,2),
  data_registro DATE,
  FOREIGN KEY (usuario_id) REFERENCES usuarios(id)
);

ADD COLUMN sexo VARCHAR(10),
ADD COLUMN problemas TEXT;
ALTER TABLE usuarios ADD UNIQUE (email);


-- Tabela de Profissionais de Saúde (Médicos, Nutricionistas, Personal Trainers)
CREATE TABLE profissionais_saude (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(255) NOT NULL,
    especialidade VARCHAR(255) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    telefone VARCHAR(15) NOT NULL,
    data_criacao TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Tabela de Relacionamento entre Usuários e Profissionais de Saúde (Ex.: Médico do Usuário)
CREATE TABLE usuarios_profissionais (
    id INT AUTO_INCREMENT PRIMARY KEY,
    usuario_id INT NOT NULL,
    profissional_id INT NOT NULL,
    data_inicio DATE NOT NULL,
    data_fim DATE DEFAULT NULL,
    FOREIGN KEY (usuario_id) REFERENCES usuarios(id),
    FOREIGN KEY (profissional_id) REFERENCES profissionais_saude(id)
);

-- Tabela de Registros de Peso e IMC (controle de peso)
CREATE TABLE controle_peso (
    id INT AUTO_INCREMENT PRIMARY KEY,
    usuario_id INT NOT NULL,
    peso DECIMAL(5,2) NOT NULL,
    imc DECIMAL(5,2) NOT NULL,
    data_registro TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (usuario_id) REFERENCES usuarios(id)
);

-- Tabela de Treinos Personalizados
CREATE TABLE treinos (
    id INT AUTO_INCREMENT PRIMARY KEY,
    usuario_id INT NOT NULL,
    nome_treino VARCHAR(255) NOT NULL,
    data_inicio DATE NOT NULL,
    data_fim DATE DEFAULT NULL,
    FOREIGN KEY (usuario_id) REFERENCES usuarios(id)
);

-- Tabela de Exercícios Realizados pelo Usuário
CREATE TABLE exercicios (
    id INT AUTO_INCREMENT PRIMARY KEY,
    treino_id INT NOT NULL,
    nome_exercicio VARCHAR(255) NOT NULL,
    duracao INT NOT NULL, -- Duração em minutos
    intensidade VARCHAR(50), -- Baixa, Média, Alta
    calorias INT DEFAULT 0,
    FOREIGN KEY (treino_id) REFERENCES treinos(id)
);

-- Tabela de Metas de Saúde do Usuário
CREATE TABLE metas_saude (
    id INT AUTO_INCREMENT PRIMARY KEY,
    usuario_id INT NOT NULL,
    meta VARCHAR(255) NOT NULL,
    valor_meta DECIMAL(10,2) NOT NULL,
    valor_atual DECIMAL(10,2) DEFAULT 0,
    prazo DATE,
    data_criacao TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (usuario_id) REFERENCES usuarios(id)
);

-- Tabela de Notificações para Lembretes e Metas
CREATE TABLE notificacoes (
    id INT AUTO_INCREMENT PRIMARY KEY,
    usuario_id INT NOT NULL,
    mensagem TEXT NOT NULL,
    tipo VARCHAR(50), -- Ex.: "Meta", "Consulta", "Lembrete"
    data_envio TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    lida BOOLEAN DEFAULT FALSE,
    FOREIGN KEY (usuario_id) REFERENCES usuarios(id)
);

-- Tabela de Playlist de Música (para treinos)
CREATE TABLE playlists (
    id INT AUTO_INCREMENT PRIMARY KEY,
    usuario_id INT NOT NULL,
    nome_playlist VARCHAR(255) NOT NULL,
    link_spotify VARCHAR(255) DEFAULT NULL, -- Link para o Spotify
    data_criacao TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (usuario_id) REFERENCES usuarios(id)
);

-- Tabela de Exercícios de Alimentação (Alimentação saudável)
CREATE TABLE alimentacao (
    id INT AUTO_INCREMENT PRIMARY KEY,
    usuario_id INT NOT NULL,
    tipo_alimento VARCHAR(255) NOT NULL, -- Ex.: Fruta, Verdura, Proteína, etc.
    quantidade DECIMAL(5,2) NOT NULL, -- Quantidade do alimento (em gramas)
    calorias INT DEFAULT 0,
    data_consumo TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (usuario_id) REFERENCES usuarios(id)
);

-- Tabela de Relatórios de Saúde (para os gráficos de progresso)
CREATE TABLE relatorios_saude (
    id INT AUTO_INCREMENT PRIMARY KEY,
    usuario_id INT NOT NULL,
    tipo_relatorio VARCHAR(255) NOT NULL, -- Ex.: Peso, IMC, Calorias
    valor DECIMAL(10,2) NOT NULL,
    data_relatorio TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (usuario_id) REFERENCES usuarios(id)
);

-- Tabela de Configurações de Usuário (como notificações, preferências)
CREATE TABLE configuracoes_usuario (
    id INT AUTO_INCREMENT PRIMARY KEY,
    usuario_id INT NOT NULL,
    notificacoes BOOLEAN DEFAULT TRUE,
    preferencias VARCHAR(255) DEFAULT NULL, -- Preferências do usuário
    data_criacao TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (usuario_id) REFERENCES usuarios(id)
);

-- Tabela de Atividades de Log do Usuário (para manter o controle de ações feitas no sistema)
CREATE TABLE logs_usuarios (
    id INT AUTO_INCREMENT PRIMARY KEY,
    usuario_id INT NOT NULL,
    acao VARCHAR(255) NOT NULL, -- Ex.: "Login", "Registro de Peso", "Treino Completo"
    data_acao TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (usuario_id) REFERENCES usuarios(id)
);

-- Tabela de Avaliação de Profissionais de Saúde (feedback de usuários)
CREATE TABLE avaliacoes_profissionais (
    id INT AUTO_INCREMENT PRIMARY KEY,
    usuario_id INT NOT NULL,
    profissional_id INT NOT NULL,
    avaliacao INT NOT NULL, -- Avaliação de 1 a 5
    comentario TEXT DEFAULT NULL,
    data_avaliacao TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (usuario_id) REFERENCES usuarios(id),
    FOREIGN KEY (profissional_id) REFERENCES profissionais_saude(id)
);





CREATE DATABASE IF NOT EXISTS healthtrack;

USE healthtrack;

CREATE TABLE IF NOT EXISTS usuarios (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    senha VARCHAR(255) NOT NULL,
    peso DECIMAL(5,2),
    altura DECIMAL(5,2)
);

CREATE TABLE IF NOT EXISTS controle_peso (
    id INT AUTO_INCREMENT PRIMARY KEY,
    usuario_id INT NOT NULL,
    peso DECIMAL(5,2),
    imc DECIMAL(5,2),
    FOREIGN KEY (usuario_id) REFERENCES usuarios(id) ON DELETE CASCADE
);

CREATE TABLE IF NOT EXISTS profissionais_saude (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    especialidade VARCHAR(100),
    email VARCHAR(100),
    telefone VARCHAR(20)
);

CREATE TABLE IF NOT EXISTS usuarios_profissionais (
    usuario_id INT NOT NULL,
    profissional_id INT NOT NULL,
    data_inicio DATE,
    PRIMARY KEY (usuario_id, profissional_id),
    FOREIGN KEY (usuario_id) REFERENCES usuarios(id),
    FOREIGN KEY (profissional_id) REFERENCES profissionais_saude(id)
);
