== Présentations ==
# Présentation du plan d'étude
# Présentation du matériel qui sera utilisé
#* PicKit4
#* Carte de développement [[media:PicKit3_Demo_Board_Schema.pdf |(Voir le schéma)]]
# Présentation de l'environnement logiciel
#* MPLAB X IDE
#* Compilateur XC8

== Structure générale d’un programme en C  ==

* Sections des #define, des prototypes,  des méthodes et du programme principal.
<source lang="c">
/**
 * @file   main.c, projet Lab1
 * @author Benoit Beaulieu
 * @date   Août 2020
 * @brief  Laboratoire 1 du cours 247-316
 * @version 1.0
 * Environnement:
 *     Développement: MPLAB X IDE (version 5.40)
 *     Compilateur: XC8 (version 2.20)
 *     Matériel: Carte démo du Pickit4. PIC 18F45K20
 */

 /****************** Liste des INCLUDES ****************************************/
#include <xc.h>
#include <stdbool.h>  // pour l'utilisation du type bool
#include "serie.h"  // pour la librairie de communication série avec un terminal sur ordinateur


/********************** CONSTANTES *******************************************/
#define _XTAL_FREQ 1000000 //Constante utilisée par __delay_ms(x). Doit = fréq interne du uC
#define GAUCHE true
#define DROITE false

/********************** PROTOTYPES *******************************************/
void initialisation(void);


/****************** VARIABLES GLOBALES ****************************************/

int g_maVarGlobale;

/*               ***** PROGRAMME PRINCPAL *****                             */
void main(void)
{
    initialisation();
    
    while(1) //boucle principale
    {        
       if (PORTDbits.RD0 == 1) //si DEL allumée
       {
          PORTDbits.RD0 = 0; //éteins DEL
       }
       else
       {
          PORTDbits.RD0 = 1; //allume DEL
       }
        
       //plus simplement: PORTDbits.RD0 = PORTDbits.RD0 ^ 1; //On change l'état de la DEL 0
        
       __delay_ms(300);
        
     }
}


/**
 * @brief Initialisation des différents registres et variables.
 * @param Aucun
 * @return Aucun
 */
void initialisation(void)
{
    TRISD = 0; //Tout le port D en sortie
    PORTD = 0; //Toutes les DEL éteintes
    //Attention! 
    ANSELH = 0;  // RB0 à RB4 en mode digital. Sur 18F45K20 AN et PortB sont sur les memes broches
    TRISB = 0xFF; //tout le port B en entree. Attention TRISB = 1 ne met que le bit 0 du registre TRISB à 1, c'est éuivalent à TRISB = 0b00000001
    
}
</source>

== Installation d'MPLAB ==
# Installez IDE MPLAB X IDE version 6.0. Disponible sur https://www.microchip.com/mplab/mplab-x-ide
# Pendant l'installation, allez chercher votre kit de pièces au magasin. 
# Installez XC8 version 2.40. Disponible sur : https://www.microchip.com/mplab/compilers, dans la section ''View All XC Compiler Downloads and Documentation''.

== Exemple 1 ==
# Téléchargez le dossier [[Media:Sem1Ex1X.zip | Sem1Ex1.zip]] sur votre disque local. 
# Lancez MPLABX et charger le projet avec le menu File, Open Project
# Ouvrez le fichier main.c
# Compilez le projet avec Production, Build Main project (F11)
# Lancez le programme avec Debug, Debug main project
# Vérifiez la fonctionnalité. Les DEL doivent afficher en binaire combien de fois on a appuyé sur SW1 et doivent clignoter après la 8ième fois.
# Arrêtez le programme et placez un point d'arrêt à la ligne 30  '''initialisation();''' utiliser la touche F7 (menu Debug, Step into) pour suivre votre programme un pas à la fois. Assurez-vous de comprendre le comportement du programme à chacune des lignes d'instructions.
# Appuyez sur SW1 et en exécutant votre programme un pas à la fois, faites passer votre programme dans la condition ''if (PORTBbits.RB0 == 0)''. Affichez l'état du port D avec le Menu Debug, New Watch ... Continuez l'exécution du programme avec F7 et faites afficher la valeur binaire 3 (0b00000011) sur les DEL. Assurez-vous de voir cette valeur sur le PORTD dans MPLAB, section ''variables''. '''Montrez au professeur'''.


<source lang="C">
/**
 * @file   main.c, projet Ex1Sem1
 * @author Benoit Beaulieu
 * @date   Aout 2019
 * @brief  Exemple #1 semaine #1. Les dels comptent en binaire à chaque fois
  * que l'on appuie sur le bouton SW1. Arrivé à 8, on fait clignoter les dels
  * indéfiniment.
  * Environnement:
 *     Développement: MPLAB X IDE (version 5.40)
 *     Compilateur: XC8 (version 2.20)
 *     Matériel: Carte démo du Pickit4. PIC 18F45K20
 */

 /****************** Liste des INCLUDES ****************************************/
#include <xc.h>

/********************** CONSTANTES *******************************************/
#define _XTAL_FREQ 1000000 //Constante utilisée par __delay_ms(x). Doit = fréq interne du uC

/********************** PROTOTYPES *******************************************/
void initialisation(void);


/****************** VARIABLES GLOBALES ****************************************/


/*               ***** PROGRAMME PRINCPAL *****                             */
void main(void)
{
    initialisation();
    while(1) //boucle principale
    {   
		//lecture du bouton sw1
        if (PORTBbits.RB0 == 0)  //si bouton appuyé?
        {
            while (PORTBbits.RB0 == 0);//on attend que le bouton soit relâché
            PORTD = PORTD + 1; //add. 1 à la valeur binaire affichée sur le port
        }
		
        if (PORTD == 8) //arrivé au compte max?
        {
			//on restera dans cette boucle à faire clignoter les dels
            while(1) 
            {
	        __delay_ms(100);
                PORTD = 0;
                __delay_ms(100);
                PORTD = 255;
            }
        }
     }
}


/**
 * @brief Initialisation des différents registres et variables.
 * @param Aucun
 * @return Aucun
 */
void initialisation(void)
{
    TRISD = 0; //Tout le port D en sortie
    PORTD = 0; //Toutes les DEL éteintes
    //Attention! 
    ANSELH = 0;  // RB0 à RB4 en mode digital. Sur 18F45K20 AN et PortB sont sur les memes broches
    TRISB = 0xFF; //tout le port B en entree
    
}


</source>
