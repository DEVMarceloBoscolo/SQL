biblioteca-sql/
│
├── README.md
├── schema.sql         # Criação do banco e tabelas
├── inserts.sql        # Dados de exemplo
├── queries.sql        # Consultas úteis
├── views.sql          # Criação de views
└── procedures.sql     # Procedures e funções



-- Criação do banco e tabelas

CREATE DATABASE IF NOT EXISTS biblioteca;
USE biblioteca;

CREATE TABLE usuarios (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    data_cadastro DATE DEFAULT CURRENT_DATE
);

CREATE TABLE livros (
    id INT AUTO_INCREMENT PRIMARY KEY,
    titulo VARCHAR(200) NOT NULL,
    autor VARCHAR(100),
    genero VARCHAR(50),
    disponivel BOOLEAN DEFAULT TRUE
);

CREATE TABLE emprestimos (
    id INT AUTO_INCREMENT PRIMARY KEY,
    id_usuario INT,
    id_livro INT,
    data_emprestimo DATE DEFAULT CURRENT_DATE,
    data_devolucao DATE,
    devolvido BOOLEAN DEFAULT FALSE,
    FOREIGN KEY (id_usuario) REFERENCES usuarios(id),
    FOREIGN KEY (id_livro) REFERENCES livros(id)
);



-- Inserindo usuários

INSERT INTO usuarios (nome, email) VALUES 
('Ana Silva', 'ana@gmail.com'),
('Carlos Souza', 'carlos@gmail.com'),
('Maria Lima', 'maria@gmail.com');

-- Inserindo livros

INSERT INTO livros (titulo, autor, genero) VALUES
('Dom Casmurro', 'Machado de Assis', 'Romance'),
('1984', 'George Orwell', 'Distopia'),
('O Hobbit', 'J.R.R. Tolkien', 'Fantasia');

-- Inserindo empréstimos

INSERT INTO emprestimos (id_usuario, id_livro, data_emprestimo, data_devolucao, devolvido) VALUES
(1, 2, '2025-05-20', '2025-06-01', TRUE),
(2, 1, '2025-05-25', NULL, FALSE);



-- Ver todos os livros disponíveis
SELECT * FROM livros WHERE disponivel = TRUE;

-- Ver histórico de empréstimos
SELECT e.id, u.nome, l.titulo, e.data_emprestimo, e.data_devolucao, e.devolvido
FROM emprestimos e
JOIN usuarios u ON e.id_usuario = u.id
JOIN livros l ON e.id_livro = l.id;

-- Livros atualmente emprestados
SELECT l.titulo, u.nome, e.data_emprestimo
FROM emprestimos e
JOIN livros l ON e.id_livro = l.id
JOIN usuarios u ON e.id_usuario = u.id
WHERE e.devolvido = FALSE;



-- View de livros emprestados atualmente
CREATE VIEW livros_emprestados AS
SELECT l.titulo, u.nome, e.data_emprestimo
FROM emprestimos e
JOIN livros l ON e.id_livro = l.id
JOIN usuarios u ON e.id_usuario = u.id
WHERE e.devolvido = FALSE;



-- Procedure para marcar devolução
DELIMITER //
CREATE PROCEDURE devolver_livro (IN p_id_emprestimo INT)
BEGIN
    UPDATE emprestimos
    SET devolvido = TRUE,
        data_devolucao = CURRENT_DATE
    WHERE id = p_id_emprestimo;


END;
//
DELIMITER ;
