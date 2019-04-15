Spring Boot Enfin ;-)
===

![center](/enfin.jpg)

<h3>Lundi</h3>

veille sur Spring Boot

Recherches stages & alternances

Préparation OPQUAST

**Objectif : Reprendre le projet java EE OnlineBank et le refaire entièrement en utilisant le framework SPRING**

**Configuration de Spring boot**

24.1 Depuis le marketplace, ajouter à Eclipse spring tools

24.2 Créer un projet spring boot : new / Spring Boot / Spring Starter Project / Config / 

- nom : myOnlineBank
- package : co.simplon

24.3 next / selectionner 6 dépendances : 
sql/jpa + sql/mysql + web/web + template engine/themeleaf + security/security + core/dev tools

24.4 dans fichier pom.xml, mettre entre commentaire la gestion securité de spring (inutile pour le moment, peut bloquer votre travail)

**Ajout des entités**

25.1 ajouter un package co.simplon.entities dans lequel vous ajouterez vos classes en respectant scrupuleusement les diagrammes de classes et donc les associations !
![center](/diag.png)

25.2 Pour chaque classe, mettre en place le mapping objet relationnel (jpa)

25.3 Verifier que la base de donnée a bien été généré, pour ce faire, vous devez d'abord spécifier les data source dans src/main/resources/application.properties puis démarrer votre appli pour tester si les tables ont bien été crées.

**couche DAO**

26.1 Mettre en place la couche DAO (co.simplon.dao) à l'aide d'un package puis ajouter les interfaces ClientRepository, CompteRepository et OperationRepository comme dans l'exemple :

    public interface ClientRepository extends JpaRepository<Client,Long> {
    }

26.2 Dans le main de votre application, implementer l'interface CommandLineRunner puis redéfinisser la méthode run dans laquelle vous ajouterez des clients, des comptes et différentes opérations (voir jeux d'essais plus bas) -> executer puis vérifier en base si c'est bon mais n'oubliez pas les attributs indispensables ici :
    
    @Autowired	//Mecanisme permettant d'injecter cet attribut afin de l'utiliser
	  private ClientRepository clientRepository;

**couche METIER**

27.1 Il faut réaliser la couche métier de votre appli(co.simplon.metier), implémenter l'interface suivante :
    
    public interface IBanqueMetier {
	    public Compte consulterCompte(String codeCpte);
	    public void verser(String codeCpte,double montant);
	    public void retirer(String codeCpte, double montant);
	    public void virement(String codeCpte1,String codeCpte2, double montant);
	    public Page<Operation> listOperation(String codeCpte, int page, int size);
    }

27.2 Afin de valider notre travail, ajouter dans votre main, un objet BanqueMetier (implémente interface IBanqueMetier) puis effectuer une opération de versement par ex et verifier en base si c'est bon (solde du compte + table des opérations)

     @Autowired
	   private IBanqueMetier banqueMetier;

Jeux d'essais :

    @Override
	  public void run(String... args) throws Exception {
      Client c1 = clientRepository.save(new Client("chris","chris@gmail.com"));
      Client c2 = clientRepository.save(new Client("maktar","maktar@gmail.com"));	

      Compte cp1 = compteRepository.save(new CompteCourant("c1",new Date(), 900,c1,60));
      Compte cp2 = compteRepository.save(new CompteEpargne("c2",new Date(), 600,c2,5.5));

      operationRepository.save(new Versement(new Date(),90,cp1));
      operationRepository.save(new Versement(new Date(),60,cp1));
      operationRepository.save(new Versement(new Date(),20,cp1));
      operationRepository.save(new Retrait(new Date(),90,cp1));

      operationRepository.save(new Versement(new Date(),19,cp2));
      operationRepository.save(new Versement(new Date(),70,cp2));
      operationRepository.save(new Versement(new Date(),10,cp2));
      operationRepository.save(new Retrait(new Date(),10,cp2));

      banqueMetier.verser("c1", 25000);
    }

<h3>Mardi</h3>

veille sur Injection de dépendances & Inversion de contrôle

Recherches stages & alternances

Préparation OPQUAST

**Couche WEB**

28.1 Pour commencer il faut ajouter un package co.simplon.web dans lequel vous devez réaliser votre classe BankController
    
    @Controller		//spring gère le controleur
    public class BankController {
    @Autowired	//pour faire l'injection des dépendances
	  private IBanqueMetier banqueMetier;	//le controleur a besoin de la couche métier
	
	  @RequestMapping("/operations")	//en cliquant sur /operations nous aurons la vue comptes.html
	  public String index() {			//retourne une vue par défaut
		  return "comptes";
	  }

28.2 Il s'agit maintenant d'ajouter au projet( rep ressources) boostrap et comptes.html(utiliser themeleaf) 


28.3 Reprenez maintenant les questions suivantes uniquement et répondez y dans notre nouveau contexte ici : 20.3, 21.1 à 21.3


<h3>Mercredi</h3>

veille sur Spring Mvc & Theameleaf

Recherches stages & alternances

CERTIF OPQUAST

29.1 Il s'agit ici de gérer la pagination de vos opérations afin d'obtenir le résultat suivant notamment lorsqu'il y a de nombreuses opérations : 
![center](/pagination.png)

<h3>jeudi</h3>

veille sur Spring Security

Recherches stages & alternances

30.1 Afin de personnaliser la gestion de la sécurité de Spring, ajouter une classe SecurityConfig héritant de  WebSecurityConfigurerAdapter puis redéfinissez les méthodes configure(AuthenticationManagerBuilder auth) et configure(HttpSecurity http) afin de définir 2 roles :

-> USER pour utilisateur qui peut consulter des comptes et leurs opérations

-> ADMIN pour administrateur qui est USER + peut effectuer des opérations

30.2 Dans la question précédente, vous avez certainement utilisé le formulaire par défaut de spring (formLogin) pour vous connecter, faites de même pour vous déconnecter.

30.3 Ajouter maintenant votre gestion personnalisé d'authentification en créant un controleur "LoginController.java" de gestion de votre page d'authentification "login.html" qu'il faudra aussi ajouter.

30.4 Il faut maintenant revenir à notre cas particulier de gestion des utilisateurs, en effet, ajouter une page 403.html appelée lorsqu'un utilisateur (USER) tente de faire des opérations accessible que pour les administrateurs (ADMIN)

30.5 Tenter maintenant de ne pas afficher le menu des opérations si l'utilisateur n'est pas un ADMIN 

30.6 Afin d'amener plus sécurité à notre projet, on souhaite dorénavant avoir nos utilisateurs ou administrateurs en base de donné aussi réfléchissez à une solution adaptée

30.7 Vérifier que l'appli est conforme aux besoins, tester pour valider toutes les fonctionnalités/options/erreurs, trouver à chaque fois des solutions adaptées, n'oubliez pas de documenter vos sources.

30.8 Déploiement de l'appli + envoi sur github

<h3>Vendredi</h3>

veille sur Jsf & Struts

Recherches stages & alternances

Point sur les projets pédagogiques

*après midi détente 
Auberge espagnole / buffet partagé
sports et jeux divers*

**Ressources**

[Spring](https://spring.io/)

[Les meilleurs cours et tutoriels pour apprendre Spring](https://spring.developpez.com/cours/#spring-boot)

[YouTube](https://youtu.be/u2z-oTMAVKY)
