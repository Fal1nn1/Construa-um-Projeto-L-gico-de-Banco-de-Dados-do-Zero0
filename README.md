# Construa-um-Projeto-L-gico-de-Banco-de-Dados-do-Zero0

-- Criação das tabelas

CREATE TABLE Clientes (
    ClienteID INT AUTO_INCREMENT PRIMARY KEY,
    Nome VARCHAR(100) NOT NULL,
    CPF VARCHAR(14) UNIQUE NOT NULL
);

CREATE TABLE Veiculos (
    VeiculoID INT AUTO_INCREMENT PRIMARY KEY,
    ClienteID INT NOT NULL,
    Placa VARCHAR(8) UNIQUE NOT NULL,
    Marca VARCHAR(50) NOT NULL,
    Modelo VARCHAR(50) NOT NULL,
    AnoFabricacao INT NOT NULL,
    FOREIGN KEY (ClienteID) REFERENCES Clientes(ClienteID)
);

CREATE TABLE Servicos (
    ServicoID INT AUTO_INCREMENT PRIMARY KEY,
    Descricao VARCHAR(200) NOT NULL,
    PrecoUnitario DECIMAL(10, 2) NOT NULL
);

CREATE TABLE OrdensServico (
    OrdemID INT AUTO_INCREMENT PRIMARY KEY,
    VeiculoID INT NOT NULL,
    DataEntrada DATE NOT NULL,
    DataSaida DATE,
    Status VARCHAR(20) NOT NULL,
    FOREIGN KEY (VeiculoID) REFERENCES Veiculos(VeiculoID)
);

CREATE TABLE ItensOrdem (
    OrdemID INT NOT NULL,
    ServicoID INT NOT NULL,
    Quantidade INT NOT NULL,
    PRIMARY KEY (OrdemID, ServicoID),
    FOREIGN KEY (OrdemID) REFERENCES OrdensServico(OrdemID),
    FOREIGN KEY (ServicoID) REFERENCES Servicos(ServicoID)
);

-- Inserção de dados de exemplo

INSERT INTO Clientes (Nome, CPF)
VALUES
    ('João Silva', '123.456.789-01'),
    ('Maria Souza', '987.654.321-02'),
    ('Pedro Almeida', '456.789.123-03');

INSERT INTO Veiculos (ClienteID, Placa, Marca, Modelo, AnoFabricacao)
VALUES
    (1, 'ABC1234', 'Fiat', 'Uno', 2010),
    (2, 'DEF5678', 'Volkswagen', 'Gol', 2015),
    (3, 'GHI9012', 'Ford', 'Fiesta', 2018);

INSERT INTO Servicos (Descricao, PrecoUnitario)
VALUES
    ('Troca de óleo', 80.00),
    ('Revisão geral', 250.00),
    ('Alinhamento', 120.00);

INSERT INTO OrdensServico (VeiculoID, DataEntrada, Status)
VALUES
    (1, '2023-05-01', 'Aberta'),
    (2, '2023-05-02', 'Concluída'),
    (3, '2023-05-03', 'Aberta');

INSERT INTO ItensOrdem (OrdemID, ServicoID, Quantidade)
VALUES
    (1, 1, 1),
    (1, 3, 1),
    (2, 2, 1),
    (3, 1, 1);

-- Consultas SQL

-- Recuperações simples com SELECT Statement
SELECT * FROM Clientes;
SELECT Marca, Modelo FROM Veiculos;

-- Filtros com WHERE Statement
SELECT * FROM OrdensServico WHERE Status = 'Concluída';
SELECT * FROM Veiculos WHERE AnoFabricacao >= 2015;

-- Expressões para gerar atributos derivados
SELECT OrdemID, SUM(Servicos.PrecoUnitario * ItensOrdem.Quantidade) AS ValorTotal
FROM ItensOrdem
JOIN Servicos ON ItensOrdem.ServicoID = Servicos.ServicoID
GROUP BY OrdemID;

-- Ordenações dos dados com ORDER BY
SELECT * FROM Veiculos ORDER BY AnoFabricacao DESC;
SELECT Nome, DataEntrada FROM OrdensServico ORDER BY DataEntrada ASC;

-- Condições de filtros aos grupos - HAVING Statement
SELECT ClienteID, COUNT(*) AS TotalOrdensServico
FROM OrdensServico
JOIN Veiculos ON OrdensServico.VeiculoID = Veiculos.VeiculoID
GROUP BY ClienteID
HAVING COUNT(*) > 1;

-- Junções entre tabelas para fornecer uma perspectiva mais complexa dos dados
SELECT
    Clientes.Nome AS NomeCliente,
    Veiculos.Marca,
    Veiculos.Modelo,
    Servicos.Descricao AS DescricaoServico,
    ItensOrdem.Quantidade
FROM Clientes
JOIN Veiculos ON Clientes.ClienteID = Veiculos.ClienteID
JOIN OrdensServico ON Veiculos.VeiculoID = OrdensServico.VeiculoID
JOIN ItensOrdem ON OrdensServico.OrdemID = ItensOrdem.OrdemID
JOIN Servicos ON ItensOrdem.ServicoID = Servicos.ServicoID;
