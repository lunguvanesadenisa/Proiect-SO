#PROIECT CENTRALIZARE SPITAL

nano data.csv

Nr.crt.,Nume,Varsta,Numar_de_telefon
1,andrei.lungu,19,0767295375
2,andrei.popescu,50,0722947386
3,anais.valeria,25,0772896537
4,lungu.vanesa,19,0751234569

nano proiect.sh
                                                                                      
#!/bin/bash
CSV_FILE="data.csv"

#Generarea unui nou id
generate_id() {
  local id
  id=$(tail -n 1 "$CSV_FILE" | awk -F, '{print $1}')
  echo $((id + 1))
}

#Crearea unei noi inregistrari
create_record() {
  local id=$(generate_id)
  local nume=$1
  local varsta=$2
  local numar_de_telefon=$3

  echo "$id,$nume,$varsta,$numar_de_telefon" >>"$CSV_FILE"
  echo "Inregistrare creata cu succes."
}

#Actualizaarea unei inregistrari
update_record() {
  local id=$1
  local nume=$2
  local varsta=$3
  local numar_de_telefon=$4

  sed -i "s/^$id,.*/$id,$nume,$varsta,$numar_de_telefon/" "$CSV_FILE"
  echo "Inregistrare actualizata cu succes."
}

#Stergerea unei inregistrari
delete_record() {
  local id=$1
  local temp_file="temp.csv"

  #Copiem toate liniile cu exceptia celei cu id-ul introdus pentru a-l sterge, intr-un fisier temporar
  awk -F, -v id="$id" '$1 != id' "$CSV_FILE" >"$temp_file"

  if [ -s "$temp_file" ]; then
    #Suprascriem fisierul initial cu cel temporar
    mv "$temp_file" "$CSV_FILE"
    echo "Inregistrare stearsa cu succes."
  else
    echo "Inregistrarea nu a fost gasita."
  fi

  #Stergem fisierul temporar
  rm "$temp_file" 2>/dev/null
}

#Verificam daca fisierul CSV(comma separated values) exista, iar daca nu, il cream
if [ ! -f "$CSV_FILE" ]; then
  touch "$CSV_FILE"
fi

#Afisarea meniului de optiuni
echo "Selectati o optiune:"
echo "1. Creati o noua inregistrare"
echo "2. Actualizati o inregistrare"
echo "3. Stergeti o inregistrare"
read -p "Introduceti optiunea dorita: " choice

case $choice in
1)
  read -p "Introduceti numele: " nume
  read -p "Introduceti varsta: " varsta
  read -p "Introduceti numarul de telefon: " numar_de_telefon
  create_record "$nume" "$varsta" "$numar_de_telefon"
  ;;
2)
  read -p "Introduceti ID-ul: " id
  read -p "Introduceti noul nume: " nume
  read -p "Introduceti noua varsta: " varsta
  read -p "Introduceti noul numar de telefon: " numar_de_telefon
  update_record "$id" "$nume" "$varsta" "$numar_de_telefon"
  ;;
3)
  read -p "Introduceti ID-ul: " id
  delete_record "$id"
  ;;
*)
  echo "Optiunea nu este valabila"
  ;;
esac

