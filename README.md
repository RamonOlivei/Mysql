# Mysql
CREATE TABLE users (
	id SERIAL PRIMARY KEY,
	nome VARCHAR(100) NOT NULL,
	idade INT CHECK (idade >=0)NOT NULL,
	email VARCHAR(100) UNIQUE NOT NULL

);

CREATE TABLE funcionarios (
	id SERIAL PRIMARY KEY,
	cargo VARCHAR(100) NOT NULL,
	salario NUMERIC(10,2)NOT NULL,
	user_id INT NOT NULL,
	FOREIGN KEY (user_id) REFERENCES users(id)

);

CREATE TABLE log_funcionarios (
	id SERIAL PRIMARY KEY,
	operacao VARCHAR(10) NOT NULL,
	funcionario_id INT,
	cargo VARCHAR(100),
	salario NUMERIC(10,2),
	user_id INT,
	username VARCHAR DEFAULT CURRENT_USER,
	data_hora TIMESTAMP DEFAULT CURRENT_TIMESTAMP
	

);

CREATE OR REPLACE FUNCTION log_funcionario()
RETURNS TRIGGER AS $$
BEGIN
	IF (TG_OP = 'INSERT') THEN
		INSERT INTO log_funcionarios (operacao, funcionario_id, cargo, salario, user_id)
		VALUES ('INSERT', NEW.id, NEW.cargo, NEW.salario, NEW.user_id );
		RETURN NEW;

	ELSIF (TG_OP = 'UPDATE') THEN
		INSERT INTO log_funcionarios (operacao, funcionario_id, cargo, salario, user_id)
		VALUES ('INSERT', NEW.id, NEW.cargo, NEW.salario, NEW.user_id );
		RETURN NEW;

	ELSIF (TG_OP = 'DELETE') THEN
		INSERT INTO log_funcionarios (operacao, funcionario_id, cargo, salario, user_id)
		VALUES ('INSERT', OLD.id, OLD.cargo, OLD.salario, OLD.user_id );
		RETURN OLD;

	END IF;

END;

$$ LANGUAGE plppgsql;

CREATE TRIGGER trigger_log_funcionario
AFTER INSERT OR UPDATE OR DELETE ON funcionarios
FOR EACH ROW
EXECUTE FUNCTION log_funcionario();


INSERT INTO users (nome, idade, email) 
	VALUES ('Isabella', 38, 'isabella@email.com' ),
		   ('Ramon', 26, 'ramon@email.com'),
		   ('Berenice', 32, 'berenice@email.com'),
		   ('Maite', 19, 'maite@email.com');


INSERT INTO funcionarios (cargo, salario, user_id)
	VALUES ('Desenvolvedor', 4500.00, 1),
		   ('Engenheiro', 7500.00, 2),
		   ('Senior', 8000.00, 3),
		   ('Estagiario', 2000.00, 4);



SELECT 
users.nome,
users.idade,
users.email,
funcionarios.cargo,
funcionarios.salario

FROM funcionarios

JOIN 
users on users.id = funcionarios.id;

	
