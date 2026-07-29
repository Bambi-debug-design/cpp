#pragma once
#include <vector>
#include <string>


#pragma once
#include "ucet.h"
#include <vector>
#include <string>


#include "ucet.h"
#include <iostream>



virtual ~Ucet();

std:: vector <double> & getter ();

virtual void analyzujUcet() const = 0;
void analyzujUcet () const override;
void Bezny_ucet :: analyzujUcet () const 
{
    int pocitadlo = 0;
    for (int i = 0; i < historie_vkladu.size() ; i++)
    {
        if (historie_vkladu[i] < 0)
        {
            cout << historie_vkladu[i] << endl;
            pocitadlo++;
        }
        cout << pocitadlo << endl;
    }
    
    
}



Bezny_ucet :: Bezny_ucet (const string & j, double p) : Ucet(j), poplatek(p) {}

bool operator==(const Bezny_ucet& other) const;
if (u1 == u2) 

    
bool Bezny_ucet :: operator== (const Bezny_ucet& other) const 
{
    return this->poplatek == other.poplatek;
}
    cout << "Ucty maji nastaveny stejny poplatek (ANO).\n";
Bezny_ucet& operator+=(double hodnota);
u1 += 500.0;
Bezny_ucet& Bezny_ucet:: operator+= (double hodnota) 
{
    this->poplatek += hodnota;
    return *this;
}
friend std::ostream& operator<<(std::ostream& os, const Bezny_ucet& ucet);
ostream& operator<<(std::ostream& os, const Bezny_ucet& ucet) 
{
    os << "BeznyUcet [" << ucet.cislo_uctu << "], transakci: " << ucet.historie_vkladu.size();
    return os;

}
cout << "Stav po vkladu 500 Kc: " << u1 << "\n"


#include <iostream>
#include <vector>
#include "ucet.h"
#include "beznyucet.h"
#include "sporiciucet.h"

using namespace std;


void nej (Ucet* neco) 

    vector <double>& pomoc = neco->Gettertransakce();

Ucet::GetCitac()

ucty.push_back(new Bezny_ucet("CZ-1111", 50.0));

vector<double> transakce1 = {1000, 500, 200, -100, -20, 300, 400};
ucty[0]->pridejTransakce(transakce1);
ucty[i]->vypis_historii();
delete ucty[i]

Stihacka s1("LOKAL-A", 6);





void odstranDrobneZavady(Letadlo* stroj) 
{
    vector<double>& staraHistorie = stroj->getter();
    vector<double> novaHistorie;

    
    
    staraHistorie = novaHistorie; // Přepsání originálu vyčištěnými daty
}



vector<double> letyViper1 = {2.5, 1.2, -0.5, 3.0, 2.0, 1.5}; // Jedna drobná závada (-0.5)
    hangary[0]->pridejhodiny(letyViper1);
    hangary.push_back(new Stihacka("VIPER-1", 4));