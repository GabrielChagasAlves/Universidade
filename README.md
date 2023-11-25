# Universidade


## 1- Utilize Stored Procedures para automatizar a inserção e seleção dos cursos;
```SQL
delimiter $
create procedure insert_cursos (
diciplina varchar (100)
)
begin 
	insert into curso (diciplina) values (diciplina);
    end$ 
    delimiter ;
```
## selecionando os cursos
```SQL
delimiter $
create procedure consulta_cursos ()
begin 
	select * 
    from curso;
end$
delimiter ;
```
![image](https://github.com/GabrielChagasAlves/Universidade/assets/125607847/b36736d9-ea30-4698-bea8-c13e038bf3d8)

## 2- O aluno possui um e-mail que deve ter seu endereço gerado automaticamente no seguinte formato: nome.sobrenome@dominio.com
```SQL
delimiter $
create trigger gerar_email_aluno
before insert on aluno
for each row
begin
	set new.email = concat(new.nome, '.', new.sobrenome, '@facens.com');
    end$
    delimiter ;
    select * from aluno;
```
![image](https://github.com/GabrielChagasAlves/Universidade/assets/125607847/15f40bd8-53c2-4ffe-b07a-18cfd64dc2bc)

## 3- Crie uma rotina que recebe os dados de um novo curso e o insere no banco de dados;
```SQL
DELIMITER $
CREATE PROCEDURE InserirNovoCurso(
    IN p_disciplina VARCHAR(100),
    IN p_aluno_id INT UNSIGNED
)
BEGIN
    -- Inserir o novo curso na tabela curso
    INSERT INTO `Universidade`.`curso` (`disciplina`, `aluno_id_aluno`) 
    VALUES (p_disciplina, p_aluno_id);
END$
DELIMITER ;
```
## COMO INSERIR UM NOVO CURSO NA TABELA
```SQL
CALL InserirNovoCurso('Historia da tecnologia', 1);
```
## 4- Crie uma função que recebe o nome de um curso e sua área, em seguida retorna o id do curso;
```SQL
delimiter $ 
create function obter_id_curso(
nome_curso varchar (100),
area_curso varchar (100)
)
returns int 
deterministic
begin 
	declare id_curso int;
    select idcurso into id_curso
    from curso
    where diciplina = nome_curso and area = area_curso;
    return id_curso;
    end$
    delimiter ;
```

## COMO CHAMAR A FUNÇÃO
```SQL
select obter_id_curso('Matemática','ciencia da computação');
```
## 5- Crie uma procedure que recebe os dados do aluno e de um curso e faz sua matrícula;
```SQL
DELIMITER $ 
CREATE PROCEDURE matricular_aluno_novo(
in nome_aluno varchar (100),
in ra_aluno varchar (100),
in email_aluno varchar(100),
in nome_curso varchar(100)
)
begin
	declare id_curso int;
    
    select idcurso into id_curso
    from curso
    where diciplina = nome_curso;
    insert into aluno (nome,sobrenome,RA,email,cursos_idcursos)
    values (nome_aluno,ra_aluno,email_aluno,id_curso);
    end$
    delimiter ;
```
## CALL NA TABELA
```SQL
CALL matricular_aluno('nome do novo aluno', 'ra', 'email','nome do curso'); 
```
## 6- Caso o aluno já esteja matriculado em um curso, essa matrícula não pode ser realizada;
```SQL
delimiter $
CREATE PROCEDURE matricular_aluno_novo(
in nome_aluno varchar (100),
in ra_aluno varchar (100),
in email_aluno varchar(100),
in nome_curso varchar(100)
)
begin
	declare id_curso int;
    
    select idcurso into id_curso
    from curso
    where diciplina = nome_curso;
    
    IF EXISTS (select 1 from aluno where ra = ra_aluno and curso_idcurso = id_curso) then 
    signal sqlstate '4500'
    set message_text = 'aluno ja matriculado.';
    else 
    insert into aluno (nome,sobrenome,RA,email,curso_idcurso)
    values (nome_aluno,ra_aluno,email_aluno,id_curso);
    end if;
    end$
    
    delimiter ;
```
## CHAMANDO A FUNÇÃO
```SQL
CALL matricular_aluno('nome do novo aluno', 'ra', 'email','nome do curso');
```

## 7 Crie o modelo lógico do exercício.
```SQL
-- MySQL Workbench Forward Engineering

SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0;
SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0;
SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION';

-- -----------------------------------------------------
-- Schema Universidade
-- -----------------------------------------------------

-- -----------------------------------------------------
-- Schema Universidade
-- -----------------------------------------------------
CREATE SCHEMA IF NOT EXISTS `Universidade` DEFAULT CHARACTER SET utf8 ;
USE `Universidade` ;

-- -----------------------------------------------------
-- Table `Universidade`.`aluno`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `Universidade`.`aluno` (
  `id_aluno` INT UNSIGNED NOT NULL AUTO_INCREMENT,
  `nome` VARCHAR(100) NULL,
  `RA` VARCHAR(100) NULL,
  `email` VARCHAR(100) NULL,
  PRIMARY KEY (`id_aluno`))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `Universidade`.`curso`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `Universidade`.`curso` (
  `id_curso` INT UNSIGNED NOT NULL AUTO_INCREMENT,
  `diciplina` VARCHAR(100) NULL,
  `aluno_id_aluno` INT UNSIGNED NOT NULL,
  PRIMARY KEY (`id_curso`, `aluno_id_aluno`),
  INDEX `fk_curso_aluno_idx` (`aluno_id_aluno` ASC) VISIBLE,
  CONSTRAINT `fk_curso_aluno`
    FOREIGN KEY (`aluno_id_aluno`)
    REFERENCES `Universidade`.`aluno` (`id_aluno`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


SET SQL_MODE=@OLD_SQL_MODE;
SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS;
SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS;

```
