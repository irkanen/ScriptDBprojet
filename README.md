#------------------------------------------------------------
#        Script de creation en dur de la base de données sous MySQL
CREATE DATABASE permet de creer la nouvelle base de données
USE permet de se positionner sur la base de données
ce type de champ entre  dièses est un champ pour les remarques
#------------------------------------------------------------

bite
CREATE DATABASE dbsite CHARACTER SET 'utf8';
USE dbsite ;

#------------------------------------------------------------
# Table: Coordonnees 
voici une table ordinaire elle ne possede qu'une clé primaire et des informations de champs
#------------------------------------------------------------

CREATE TABLE Coordonnees(
        id_habitation      Int  Auto_increment  NOT NULL ,
        adresse_habitation Varchar (255) NOT NULL ,
        code_postal        Int NOT NULL ,
        tel_fixe           Char (10) NOT NULL ,
        tel_portable       Char (10) NOT NULL
        ,CONSTRAINT Coordonnees_PK PRIMARY KEY (id_habitation)
)ENGINE=InnoDB;

#------------------------------------------------------------
# Table: Entites
ici une autre table (qui possedait une relation 1,1 -1,n )
la relation est de type maitre esclave et pour exprimer cette relation
on inclue une clé etrangere (ou foreign key) qui pointe la table associée
on la decrit ici car la table entité est la table maitresse 
on dit que l'on fait glisser la clef etrangere
#------------------------------------------------------------

CREATE TABLE Entites(
        id_entite     Int  Auto_increment  NOT NULL ,
        nom_entite    Char (255) NOT NULL ,
        prenom_entite Char (255) NOT NULL ,
        login_entite  Varchar (255) NOT NULL ,
        pass_entite   Varchar (255) NOT NULL ,
        id_habitation Int NOT NULL
        ,CONSTRAINT Entites_PK PRIMARY KEY (id_entite)

        ,CONSTRAINT Entites_Coordonnees_FK FOREIGN KEY (id_habitation) REFERENCES Coordonnees(id_habitation)
)ENGINE=InnoDB;


#------------------------------------------------------------
# Table: Type_entites
#------------------------------------------------------------

CREATE TABLE Type_entites(
        id_type Int  Auto_increment  NOT NULL ,
        qualite Varchar (255) NOT NULL
        ,CONSTRAINT Type_entites_PK PRIMARY KEY (id_type)
)ENGINE=InnoDB;



#------------------------------------------------------------
# Table: Classer
Ceci est un autre type de table : elle est représentée par un verbe
et elle associe deux tables de cardinalités 0,n ou 1,n
On est donc forcé de creer une table possedant les deux clés 
primaires des autres tables qui deviendront sa clé principale
ces tables peuvent avoir des propriétés supplémentaires 
#------------------------------------------------------------

CREATE TABLE Classer(
        id_type   Int NOT NULL ,
        id_entite Int NOT NULL
        ,CONSTRAINT Classer_PK PRIMARY KEY (id_type,id_entite)

        ,CONSTRAINT Classer_Type_entites_FK FOREIGN KEY (id_type) REFERENCES Type_entites(id_type)
        ,CONSTRAINT Classer_Entites0_FK FOREIGN KEY (id_entite) REFERENCES Entites(id_entite)
)ENGINE=InnoDB;




#------------------------------------------------------------
# Table: Droits
#------------------------------------------------------------

CREATE TABLE Droits(
        id_droit      Int  Auto_increment  NOT NULL ,
        libelle_droit Varchar (255) NOT NULL ,
        isok_droit    Bool NOT NULL COMMENT "determine la possession du droit ou non" 
        ,CONSTRAINT Droits_PK PRIMARY KEY (id_droit)
)ENGINE=InnoDB;


#------------------------------------------------------------
# Table: Entites
#------------------------------------------------------------

CREATE TABLE Entites(
        id_entite     Int  Auto_increment  NOT NULL ,
        nom_entite    Char (255) NOT NULL ,
        prenom_entite Char (255) NOT NULL ,
        login_entite  Varchar (255) NOT NULL ,
        pass_entite   Varchar (255) NOT NULL ,
        id_habitation Int NOT NULL
        ,CONSTRAINT Entites_PK PRIMARY KEY (id_entite)

        ,CONSTRAINT Entites_Coordonnees_FK FOREIGN KEY (id_habitation) REFERENCES Coordonnees(id_habitation)
)ENGINE=InnoDB;


#------------------------------------------------------------
# Table: Evenements
#------------------------------------------------------------

CREATE TABLE Evenements(
        id_evenement      Int  Auto_increment  NOT NULL ,
        libelle_evenement Varchar (255) NOT NULL ,
        date_evenement    Date NOT NULL
        ,CONSTRAINT Evenements_PK PRIMARY KEY (id_evenement)
)ENGINE=InnoDB;


#------------------------------------------------------------
# Table: Type_documents
#------------------------------------------------------------

CREATE TABLE Type_documents(
        id_typedoc        Int  Auto_increment  NOT NULL ,
        libelle_typedoc   Varchar (255) NOT NULL ,
        extension_typedoc Varchar (4) NOT NULL
        ,CONSTRAINT Type_documents_PK PRIMARY KEY (id_typedoc)
)ENGINE=InnoDB;


#------------------------------------------------------------
# Table: Documents
#------------------------------------------------------------

CREATE TABLE Documents(
        Id_document      Int  Auto_increment  NOT NULL ,
        libelle_document Varchar (255) NOT NULL ,
        id_entite        Int NOT NULL ,
        id_typedoc       Int NOT NULL
        ,CONSTRAINT Documents_PK PRIMARY KEY (Id_document)

        ,CONSTRAINT Documents_Entites_FK FOREIGN KEY (id_entite) REFERENCES Entites(id_entite)
        ,CONSTRAINT Documents_Type_documents0_FK FOREIGN KEY (id_typedoc) REFERENCES Type_documents(id_typedoc)
)ENGINE=InnoDB;


#------------------------------------------------------------
# Table: Type_taches
#------------------------------------------------------------

CREATE TABLE Type_taches(
        id_typetache      Int  Auto_increment  NOT NULL ,
        Libelle_typetache Varchar (4) NOT NULL
        ,CONSTRAINT Type_taches_PK PRIMARY KEY (id_typetache)
)ENGINE=InnoDB;


#------------------------------------------------------------
# Table: Taches
#------------------------------------------------------------

CREATE TABLE Taches(
        id_tache      Int  Auto_increment  NOT NULL ,
        libelle_tache Varchar (255) NOT NULL ,
        id_typetache  Int NOT NULL
        ,CONSTRAINT Taches_PK PRIMARY KEY (id_tache)

        ,CONSTRAINT Taches_Type_taches_FK FOREIGN KEY (id_typetache) REFERENCES Type_taches(id_typetache)
)ENGINE=InnoDB;


#------------------------------------------------------------
# Table: Posseder

#------------------------------------------------------------

CREATE TABLE Posseder(
        id_type  Int NOT NULL ,
        id_droit Int NOT NULL
        ,CONSTRAINT Posseder_PK PRIMARY KEY (id_type,id_droit)

        ,CONSTRAINT Posseder_Type_entites_FK FOREIGN KEY (id_type) REFERENCES Type_entites(id_type)
        ,CONSTRAINT Posseder_Droits0_FK FOREIGN KEY (id_droit) REFERENCES Droits(id_droit)
)ENGINE=InnoDB;


#------------------------------------------------------------
# Table: Composer (assoc)
#------------------------------------------------------------

CREATE TABLE Composer(
        id_evenement   Int NOT NULL ,
        id_tache Int NOT NULL
        ,CONSTRAINT Composer_PK PRIMARY KEY (id_evenement,id_tache)

        ,CONSTRAINT Composer_Evenements_FK FOREIGN KEY (id_evenement) REFERENCES Evenements(id_evenement)
        ,CONSTRAINT Composer_Taches0_FK FOREIGN KEY (id_tache) REFERENCES Taches(id_tache)
)ENGINE=InnoDB;



#------------------------------------------------------------
# Table: Concerner (assoc)
#------------------------------------------------------------

CREATE TABLE Concerner(
        id_entite   Int NOT NULL ,
        id_tache Int NOT NULL
        ,CONSTRAINT Concerner_PK PRIMARY KEY (id_entite,id_tache)

        ,CONSTRAINT Concerner_Entites_FK FOREIGN KEY (id_entite) REFERENCES Entites(id_entite)
        ,CONSTRAINT Concerner__Taches0_FK FOREIGN KEY (id_tache) REFERENCES Taches(id_tache)
)ENGINE=InnoDB;



#------------------------------------------------------------
# Table: Necessiter (assoc)
#------------------------------------------------------------

CREATE TABLE Necessiter(
        id_typetache   Int NOT NULL ,
        id_document Int NOT NULL
        ,CONSTRAINT Necessiter_PK PRIMARY KEY (id_typetache,id_document)

        ,CONSTRAINT Necessiter_Type_taches_FK FOREIGN KEY (id_typetache) REFERENCES Type_taches(id_typetache)
        ,CONSTRAINT Necessiter_Documents0_FK FOREIGN KEY (id_document) REFERENCES Documents(id_document)
)ENGINE=InnoDB;

#------------------------------------------------------------
# Table: Permettregestion (assoc)
#------------------------------------------------------------

CREATE TABLE Permettregestion(
        id_typetache   Int NOT NULL ,
        id_droit Int NOT NULL
        ,CONSTRAINT Permettregestion_PK PRIMARY KEY (id_typetache,id_droit)

        ,CONSTRAINT Permettregestion_Type_taches_FK FOREIGN KEY (id_typetache) REFERENCES Type_taches(id_typetache)
        ,CONSTRAINT Permettregestion_Droits0_FK FOREIGN KEY (id_droit) REFERENCES Droits(id_droit)
)ENGINE=InnoDB;

#------------------------------------------------------------
# Table: Octroyer (assoc)
#------------------------------------------------------------

CREATE TABLE Octroyer(
        id_type   Int NOT NULL ,
        id_droit Int NOT NULL
        ,CONSTRAINT Octroyer_PK PRIMARY KEY (id_type,id_droit)

        ,CONSTRAINT Octroyer_Type_entites_FK FOREIGN KEY (id_type) REFERENCES Type_entites(id_type)
        ,CONSTRAINT Octroyer_Droits0_FK FOREIGN KEY (id_droit) REFERENCES Droits(id_droit)
)ENGINE=InnoDB;


#-------------------------------------------------------------
# Test d'une table associative
 je cree 3 types de postes et ensuite je donne le rang programmeur a l'user dont l'iD est 1
        et le rang 3 a l'user 2
        INSERT sert a rentrer les valeurs en DUR

#--------------------------------------------------------------
INSERT INTO Type_entites 
VALUES (1,'programmeur');

INSERT INTO Type_entites 
VALUES (2,'administateur');

INSERT INTO Type_entites 
VALUES (3,'membre');

INSERT INTO Classer
VALUES (1 , 1);

INSERT INTO Classer
VALUES (3 , 2);


#------------------------------------------------------------
# Je recherche (SELECT) tout element (*) du tableau entité 
        possedant la qualité est programmeur
        Les jointures permettent d'unir les differents tableaux ayant des liens logiques 
        cela permet de tester les clés secondaires



        second test je recherche les membres de base
#------------------------------------------------------------

SELECT * FROM entites 
INNER JOIN classer ON entites.id_entite = classer.id_entite
INNER JOIN type_entites ON type_entites.id_type = classer.id_type
WHERE type_entites.qualite = 'programmeur';

SELECT * FROM entites 
INNER JOIN classer ON entites.id_entite = classer.id_entite
INNER JOIN type_entites ON type_entites.id_type = classer.id_type
WHERE type_entites.qualite = 'membre';






