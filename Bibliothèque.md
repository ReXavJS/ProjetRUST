use std::io;

#[derive(Debug)]
struct Livre {
    titre: String,
    auteur: String,
    annee: u32,
    disponible: bool,
}

fn ajouter_livre(bibliotheque: &mut Vec<Livre>) {
    let mut titre = String::new();
    let mut auteur = String::new();
    let mut annee_str = String::new();

    println!("Titre du livre : ");
    io::stdin().read_line(&mut titre).unwrap();
    let titre = titre.trim().to_string();

    if bibliotheque.iter().any(|l| l.titre == titre) {
        println!("Erreur : ce livre existe déjà");
        return;
    }

    println!("Auteur : ");
    io::stdin().read_line(&mut auteur).unwrap();
    let auteur = auteur.trim().to_string();

    println!("Année de publication : ");
    io::stdin().read_line(&mut annee_str).unwrap();
    let annee: u32 = match annee_str.trim().parse() {
        Ok(num) => num,
        Err(_) => {
            println!("Erreur : année non valide");
            return;
        }
    };

    let livre = Livre {
        titre,
        auteur,
        annee,
        disponible: true,
    };

    bibliotheque.push(livre);
    println!("Le livre a bien été ajouté");
}

fn emprunter_livre(bibliotheque: &mut Vec<Livre>) {
    let mut titre = String::new();
    println!("Titre du livre à emprunter : ");
    io::stdin().read_line(&mut titre).unwrap();
    let titre = titre.trim();

    match bibliotheque.iter_mut().find(|l| l.titre == titre) {
        Some(livre) if livre.disponible => {
            livre.disponible = false;
            println!("Emprunt validé");
        }
        Some(_) => println!("Ce livre est déjà emprunté"),
        None => println!("Ce livre n'est pas dans la bibliothèque"),
    }
}

fn retourner_livre(bibliotheque: &mut Vec<Livre>) {
    let mut titre = String::new();
    println!("Titre du livre à retourner : ");
    io::stdin().read_line(&mut titre).unwrap();
    let titre = titre.trim();

    match bibliotheque.iter_mut().find(|l| l.titre == titre) {
        Some(livre) if !livre.disponible => {
            livre.disponible = true;
            println!("Livre bien retourné");
        }
        Some(_) => println!("Ce livre n'est pas emprunté et est donc disponible"),
        None => println!("Ce livre n'est pas dans la bibliothèque"),
    }
}

fn afficher_tous_les_livres(bibliotheque: &Vec<Livre>) {
    if bibliotheque.is_empty() {
        println!("Bibliothèque vide");
        return;
    }

    println!("Liste des livres : ");
    for livre in bibliotheque {
        println!(
            "Titre : \"{}\", Auteur : \"{}\", Année de parution : \"{}\", Statut : {}",
            livre.titre,
            livre.auteur,
            livre.annee,
            if livre.disponible { "Disponible" } else { "Emprunté" }
        );
    }
}


fn afficher_livres_disponibles(bibliotheque: &Vec<Livre>) {
    let disponibles: Vec<&Livre> = bibliotheque.iter().filter(|l| l.disponible).collect();

    if disponibles.is_empty() {
        println!("Bibliothèque vide");
        return;
    }

    println!("Livres disponibles :");
    for livre in disponibles {
        println!(
            "Titre : \"{}\", Auteur : \"{}\", Année de parution : \"{}\"",
            livre.titre,
            livre.auteur,
            livre.annee
        );
    }
}


fn main() {
    let mut bibliotheque: Vec<Livre> = Vec::new();

    loop {
        println!("Bibliothèque");
        println!("1. Ajouter un livre");
        println!("2. Emprunter un livre");
        println!("3. Retourner un livre");
        println!("4. Afficher tous les livres");
        println!("5. Afficher les livres disponibles");
        println!("6. Quitter");

        let mut choix = String::new();
        io::stdin().read_line(&mut choix).unwrap();

        match choix.trim() {
            "1" => ajouter_livre(&mut bibliotheque),
            "2" => emprunter_livre(&mut bibliotheque),
            "3" => retourner_livre(&mut bibliotheque),
            "4" => afficher_tous_les_livres(&bibliotheque),
            "5" => afficher_livres_disponibles(&bibliotheque),
            "6" => {
                println!("Fermeture");
                break;
            }
            _ => println!("Erreur : cette fonctionalité n'existe pas"),
        }
    }
}

