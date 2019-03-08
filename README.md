# 1. Introducció
A continuació és descriu el funcionament i les diferents modalitats de consum del servei de Representa.

## 1.1. Integració PCI
Tal i com es reflecteix a la següent figura, el **Core** de **Representa** s'integra dins de l'arquitectura de la Plataforma de Col·laboració Interadministrativa (en endavant PCI) a mode d'un nou servei accessible a través de la MTI.

Per tant els integradors que vulguin accedir al Core de Representa ho hauran de fer a través de la missatgeria de la PCI utilitzant l'element `<DatosEspecificos>` d'aquesta, per a més informació podeu consultar el document d'integració de la PCI [aqui](https://www.aoc.cat/knowledge-base/plataforma-de-col-laboracio-administrativa-2/idservei/enotum/)

<p align="center">
<img align="center" src="img/representa_arquitectura_pci.png" />
</p>  

# 2. Missatgeria
Com es comenta en el punt 1.1 d'aquest document, Representa funciona com a servei dins de la PCI, serà per tant necessari treballar amb la missatgeria de la PCI, encapsulant la missatgeria específica de Representa dins d'aquesta.

Específicament per a fer ús del servei de Representa dins de la missatgeria de la PCI és necessari informar els següents elements del missatge XML:

* //Peticion/Atributos/CodigoProducto >> REPRESENTA
* //Peticion/Atributos/CodigoCertificado >> REPRESENTA_VALIDACIO / REPRESENTA_CONSULTA_REPRESENTACIO / REPRESENTA_CONSULTA_REPRESENTACIONS  segons l'ús**
* //Peticion/Atributos/DatosAutorizacion/Finalidad >> PROVES (preproducció) / REPRESENTA (producció)
* //Peticion/Solicitudes/SolicitudTransmision/DatosGenericos/Transmision/CodigoCertificado >> REPRESENTA_VALIDACIO / REPRESENTA_CONSULTA_REPRESENTACIO / REPRESENTA_CONSULTA_REPRESENTACIONS  segons l'ús**
* //Peticion/Solicitudes/SolicitudTransmision/DatosEspecíficos >> _Petició XML específica de *Representa*_

Pel que fa a la resta del missatge PCI, cal que aquest compleixi amb els requisits definits al document d'integració de la PCI [aqui](https://www.aoc.cat/knowledge-base/plataforma-de-col-laboracio-administrativa-2/idservei/enotum/)

** Modalitats de consum descrites a l'apartat [3.6]


# 3. Missatgeria específica
La missatgeria específica de Representa exposada a la PCI s'engloba al fitxer [dadesEspecifiques.xsd](dadesEspecifiques.xsd)


## 3.1 Representacio

```xml
<xs:complexType name="representacio">
        <xs:sequence>
            <xs:element name="identificadorLegal" type="xs:string" minOccurs="0"/>
            <xs:element name="tipusRepresentacio" type="tipusRepresentacio" minOccurs="0"/>
            <xs:element name="estat" type="estat" minOccurs="0"/>
            <xs:element name="poderdant" type="persona" minOccurs="1"/>
            <xs:element name="representant" type="persona" minOccurs="1"/>
            <xs:element name="ambitRepresentacio" type="ambitRepresentacio" minOccurs="0"/>
            <xs:element name="dataCreacio" type="xs:string" minOccurs="0"/>
            <xs:element name="dataIniciVigencia" type="xs:string" minOccurs="0"/>
            <xs:element name="dataFiVigencia" type="xs:string" minOccurs="0"/>
            <xs:element name="validacions" type="xs:int" minOccurs="0" nillable="true"/>
            <xs:element name="csvPoderNotarial" type="xs:string" minOccurs="0"/>
            <xs:element name="administracioReceptora" type="administracio" minOccurs="0"/>
            <xs:element name="refAlta" type="xs:string" minOccurs="0"/>
            <xs:element name="evidencies" minOccurs="0">
                <xs:complexType>
                    <xs:sequence>
                        <xs:element name="evidencia" type="evidencia" maxOccurs="unbounded"/>
                    </xs:sequence>
                </xs:complexType>
            </xs:element>
        </xs:sequence>
</xs:complexType>
```

|Camp | Descripció|
|---- | ----------|
|identificadorLegal | Identificador únic de la representació|
|tipusRepresentacio | Tipus de Representacio (TIPUS_A, TIPUS_B, TIPUS_C)|
|estat | Llistat d'element _estat_|
|poderdant | Persona (física o jurídica) que atorga el poder de representació|
|representant | Persona (física o jurídica) que rep el poder de representació|
|ambitRepresentacio | Element per definir l'ambit de la representació. Segons el _tipusRepresentacio_ s'inclou l'element _administracio_, _tramit_ i _capacitat_|
|dataCreacio | Data de creació de la representació|
|dataIniciVigencia | Data d'inici de vigència|
|dataFiVigencia | Data de fi de la vigència|
|validacions | Nombre de validacions realitzades sobre aquesta representació|
|csvPoderNotarial | CSV del poder notarial adjunt en la inscripció de la representació|
|administracioReceptora | Administració receptora on s'ha inscrit la representació|
|refAlta | Identificador per vincular les possibles N representacions a tramit creades en el mateix instant d'inscripció|
|evidencies | Llistat d'elements _evidencia_|

## 3.2 Evidencia
Per cada acció realitzada sobre una representació es registra un element _evidencia_ on es recull informació relativa a qui, què i quan s'ha efectuat un canvi en la representació.

```xml
<xs:complexType name="evidencia">
        <xs:sequence>
            <xs:element name="dataCreacio" type="xs:string" minOccurs="0"/>
            <xs:element name="identificadorLegal" type="xs:string"/>
            <xs:element name="motiu" type="xs:string"  minOccurs="0"/>
            <xs:element name="administracioReceptora" type="administracio" minOccurs="0"/>
            <xs:element name="funcionariReceptor" type="persona"/>
            <xs:element name="documentsEvidencia" minOccurs="0">
                <xs:complexType>
                    <xs:sequence>
                        <xs:element name="documentEvidencia" type="documentEvidencia" maxOccurs="unbounded"/>
                    </xs:sequence>
                </xs:complexType>
            </xs:element>
        </xs:sequence>
</xs:complexType>
```

|Camp | Descripció|
|---- | ----------|
|dataCreacio | Data de creació de l'evidencia|
|identificadorLegal | Identificador únic de la representació|
|motiu | Motiu del canvi|
|administracioReceptora | Element del tipus _administracio_ on s'ha efectuat el canvi|
|funcionariReceptor | Persona funcionaria que ha executat el canvi|
|documentsEvidencia | Llistat d'elements _documentEvidencia_|

## 3.3 DocumentEvidencia
Cada document adjunt a una representació es vincula a un element _evidencia_. Els documents poden ser sol·licituds (paper escanejat en còpia autèntica amb la sol·licitud de l'acció a realitzar sobre la representació), poders (poder notarial) o altres.
```xml
<xs:complexType name="documentEvidencia">
        <xs:sequence>
            <xs:element name="uuid" type="xs:string"  minOccurs="0"/>
            <xs:element name="tipusDocument" type="xs:string" minOccurs="0"/>
            <xs:element name="nomDocument" type="xs:string" minOccurs="0"/>
            <xs:element name="descripcio" type="xs:string" minOccurs="0"/>
            <xs:element name="tamany" type="xs:string" minOccurs="0"/>
            <xs:element name="tipusEncriptacio" type="tipusEncriptacio"  minOccurs="0"/>
            <xs:element name="resumCriptografic" type="xs:string"  minOccurs="0"/>
            <xs:element name="dataCreacio" type="xs:string" minOccurs="0"/>
        </xs:sequence>
</xs:complexType>
```

## 3.4 Tipus de representacions					   
```xml
<xs:simpleType name="tipusRepresentacio">
        <xs:restriction base="xs:string">
            <xs:enumeration value="TIPUS_A"/>
            <xs:enumeration value="TIPUS_B"/>
            <xs:enumeration value="TIPUS_C"/>
        </xs:restriction>
</xs:simpleType>
```
El tipus de representació té afectació en l'ambit de la representació
```xml
<xs:complexType name="ambitRepresentacio">
        <xs:sequence>
            <xs:element name="administracio" type="administracio" minOccurs="0"/>
            <xs:element name="tramit" minOccurs="0"  type="tramit"/>
            <xs:element name="capacitats" minOccurs="0" >
                <xs:complexType>
                    <xs:sequence>
                        <xs:element name="capacitat" type="capacitat" maxOccurs="unbounded"/>
                    </xs:sequence>
                </xs:complexType>
            </xs:element>
        </xs:sequence>
</xs:complexType>
```

### 3.4.1 General
Representació per a qualsevol tràmit, per a qualsevol administració.
Per aquest tipus de representació l'element _tipusRepresentacio_ té el valor **TIPUS_A**.

L'element _ambitRepresentació_ està buit ja que no aplica.

Exemple:
```xml
<representacio>
   <identificadorLegal>201900000096</identificadorLegal>
   <tipusRepresentacio>TIPUS_A</tipusRepresentacio>
   <estat>VALIDA</estat>
   <poderdant>
      ...
   </poderdant>
   <representant>
      ...
   </representant>
   <ambitRepresentacio/>
   <dataCreacio>2019-03-07T00:00:00</dataCreacio>
   <dataIniciVigencia>2019-03-06T00:00:00</dataIniciVigencia>
   <dataFiVigencia>2019-03-07T00:00:00</dataFiVigencia>
   <validacions>0</validacions>
   <administracioReceptora>
      ...
   </administracioReceptora>
   <evidencies>
      <evidencia>
          ...
      </evidencia>
   </evidencies>
</representacio>
```

### 3.4.2 A organisme
Representació per a qualsevol tràmit, per a una administració concreta.
Per aquest tipus de representació l'element _tipusRepresentacio_ té el valor **TIPUS_B**.

L'element _ambitRepresentació_ té definit l'element _administracio_.

Exemple:
```xml
<representacio>
   <identificadorLegal>201900000097</identificadorLegal>
   <tipusRepresentacio>TIPUS_A</tipusRepresentacio>
   <estat>VALIDA</estat>
   <poderdant>
      ...
   </poderdant>
   <representant>
      ...
   </representant>
   <ambitRepresentacio>
      <administracio>
	        ...
      </administracio>
   </ambitRepresentacio>
   <dataCreacio>2019-03-07T00:00:00</dataCreacio>
   <dataIniciVigencia>2019-03-06T00:00:00</dataIniciVigencia>
   <dataFiVigencia>2019-03-07T00:00:00</dataFiVigencia>
   <validacions>0</validacions>
   <administracioReceptora>
      ...
   </administracioReceptora>
   <evidencies>
      <evidencia>
          ...
      </evidencia>
   </evidencies>
</representacio>
```

### 3.4.3 A tramit
Representació per a una administració concreta i per a un tramit concret.
Per aquest tipus de representació l'element _tipusRepresentacio_ té el valor **TIPUS_C**.

L'element _ambitRepresentació_ té definida la _administracio_, l'element _tramit_ i una llista d'elements _capacitat_.

Exemple:
```xml
<representacio>
   <identificadorLegal>201900000098</identificadorLegal>
   <tipusRepresentacio>TIPUS_C</tipusRepresentacio>
   <estat>VALIDA</estat>
   <poderdant>
      ...
   </poderdant>
   <representant>
      ...
   </representant>
   <ambitRepresentacio>
      <administracio>
	        ...
      </administracio>
      <tramit>
         ...
      </tramit>
      <capacitats>
         <capacitat>
            ...
         </capacitat>
      </capacitats>
   </ambitRepresentacio>
   <dataCreacio>2019-03-07T00:00:00</dataCreacio>
   <dataIniciVigencia>2019-03-06T00:00:00</dataIniciVigencia>
   <dataFiVigencia>2019-03-07T00:00:00</dataFiVigencia>
   <validacions>0</validacions>
   <administracioReceptora>
      ...
   </administracioReceptora>
   <evidencies>
      <evidencia>
          ...
      </evidencia>
   </evidencies>
</representacio>
```

## 3.5 Estats
Estats possibles d'una representació:
* VALIDA >> Representació vàlida. L'únic estat d'una representació per a que en una consulta de validació sigui utilitzada.
* PENDENT_VALIDACIO >> S'ha aportat documentació que cal revisar i validar per poder canviar l'estat a VALIDA o DENEGADA. Quan es faci una consulta de _Validacio_ una representació en aquest estat no podrà ser usada per respondre afirmativament.
* DENEGADA >> Un cop revisada la documentació adjunta a la inscripció.
* EXPIRADA >> Una representació que abans ha estat VALIDA o PENDENT_VALIDACIO, però en la data actual està fora del seu periode de vigència.
* RENUNCIADA >> El representant renuncia a la representació.
* REVOCADA >> El poderdant revoca la representació.
* ANULADA >> Estat excepcional en que per un motiu un funcionari habilitat invalida una representació.

### 3.5.1 Diagrama de fluxe d'estats
_Pendent_


## 3.6 Operacions

### 3.6.1 Consulta de representacions
Permet consultar les representacions existents entre dues persones. 
Cal indicar a l'atribut _CodigoCertificado_ de la petició de la PCI *REPRESENTA_CONSULTA_REPRESENTACIONS*.

#### Peticio
```xml
<xs:element name="consultarRepresentacions">
        <xs:complexType>
            <xs:sequence>
                <xs:element name="ConsultaRepresentacions">
                    <xs:complexType>
                        <xs:sequence>
                            <xs:element name="mida" type="mida"/>
                            <xs:element name="pagina" type="pagina"/>
                            <xs:element name="representacio" type="representacio"/>
                            <xs:element name="estats" minOccurs="0">
                                <xs:complexType>
                                    <xs:sequence>
                                        <xs:element name="estat"  type="estat" minOccurs="0" maxOccurs="unbounded"/>
                                    </xs:sequence>
                                </xs:complexType>
                            </xs:element>
                        </xs:sequence>
                    </xs:complexType>
                </xs:element>
            </xs:sequence>
        </xs:complexType>
    </xs:element>
```
Segons el tipus de representació que es desitgi consultar, per a l'element _representacio_ serà obligatori informat determinats camps. Alguns camp es controlen mitjançant validació contra l'esquema xsd i d'altres es comproven a nivell d'aplicació segons el cas d'ús.


Camp | Descripció
---- | ----------
mida | Nombre màxim de resultats retornats per pàgina
pagina | Pàgina de resultats sol·licitada (de 0 a N)
representacio | Element _representacio_ on es poden definir alguns atributs per cercar i filtrar les representacions
estats | Llistat d'1 o més elements _estat_ usats per filtrar els resultats


#### Resposta

```xml
<xs:element name="consultarRepresentacionsResponse">
	<xs:complexType>
		<xs:sequence>
			<xs:element name="resultat">
				<xs:complexType>
					<xs:sequence>
						<xs:element name="resposta"  type="resposta"/>
						<xs:element name="numRepresentacionsTotal"/>
						<xs:element name="numPaginesTotal"/>
						<xs:element name="representacions" minOccurs="0">
							<xs:complexType>
								<xs:sequence>
									<xs:element name="representacio"  type="representacio" minOccurs="0" maxOccurs="unbounded"/>
								</xs:sequence>
							</xs:complexType>
						</xs:element>
					</xs:sequence>
				</xs:complexType>
			</xs:element>
		</xs:sequence>
	</xs:complexType>
</xs:element>
```

Camp | Descripció
---- | ----------
resposta | Element del tipus _resposta_
numRepresentacionsTotal | Nombre de representacions totals resultants de la petició de consulta
numPaginesTotal | Nombre de pàgines totals dels resultats de la petició de consulta
representacions | Llistat d'1 o més elements del tipus [_representacio_](#21-representacio)


### 3.6.2 Consulta de representació
Permet recuperar *una* representació a partir del seu identificador únic (identificadorLegal).
Cal indicar a l'atribut _CodigoCertificado_ de la petició de la PCI *REPRESENTA_CONSULTA_REPRESENTACIO*.

### Petició

```xml
<xs:element name="consultarRepresentacio">
	<xs:complexType>
        <xs:sequence>
            <xs:element name="ConsultaRepresentacio">
                <xs:complexType>
                    <xs:sequence>
                        <xs:element name="identificadorLegal" type="xs:string" />
                    </xs:sequence>
                </xs:complexType>
            </xs:element>
        </xs:sequence>
    </xs:complexType>
</xs:element>
```

Camp | Descripció
---- | ----------
identificadorLegal | Identificador únic de la representació


### Resposta

```xml
<xs:element name="consultarRepresentacioResponse">
	<xs:complexType>
		<xs:sequence>
			<xs:element name="resultat">
				<xs:complexType>
					<xs:sequence>
						<xs:element name="resposta" type="resposta" />
						<xs:element name="representacio" type="representacio" minOccurs="0" />
					</xs:sequence>
				</xs:complexType>
			</xs:element>
		</xs:sequence>
	</xs:complexType>
</xs:element>
```

Camp | Descripció
---- | ----------
resposta | Identificador únic de la representació
representacio | Element del tipus [_representació_](#21-representacio)


### 3.6.2 Validació
La opertació de validació permet preguntar a Representa si existeix alguna representació en estat _VALIDA_ entre un poderdant i un representant per un tràmit i capacitat específic.
En cas d'existir alguna representació existent i vàlida que ho permeti es respon afirmativament i es retorna el detall de la representació emprada per donar aquesta resposta.
Cal indicar a l'atribut _CodigoCertificado_ de la petició de la PCI *REPRESENTA_VALIDACIO*.

#### Petició

```xml
<xs:element name="validarRepresentacio">
        <xs:complexType>
            <xs:sequence>
                <xs:element name="representacio" type="representacio" minOccurs="0"/>
                <xs:element name="dataValidacio" type="xs:string"/>
                <xs:element name="funcionariSolicitant" type="persona"/>
                <xs:element name="administracioSolicitant" type="administracio"/>
            </xs:sequence>
        </xs:complexType>
</xs:element>
```

Camp | Descripció
---- | ----------
representacio | Element del tipus _representacio_ on incloure la informació relativa a la consulta de validació (poderdant, representant, tramit, capacitat/s)
dataValidacio | Data on es fa la consulta de validació. Aquesta data ha d'estar compresa entre la _dataIniciVigencia_ i la _dataFiVigencia_ de la representació recuperada pel servei
funcionariSolicitant | Persona funcionaria que fa la consulta de validació
administracioSolicitant | Administració des d'on es llença la consulta de validació

#### Resposta

```xml
<xs:element name="validarRepresentacioResponse">
        <xs:complexType>
            <xs:sequence>
                <xs:element name="resultat">
                    <xs:complexType>
                        <xs:sequence>
                            <xs:element name="resposta"  type="resposta"/>
                            <xs:element name="representacio" type="representacio"/>
                        </xs:sequence>
                    </xs:complexType>
                </xs:element>
            </xs:sequence>
        </xs:complexType>
</xs:element>
```

Camp | Descripció
---- | ----------
resposta | Element del tipus _resposta_
representacio | Element del tipus _representacio_ on es retorna el detall de la representació vàlida que permet respondre afirmativament a la consulta de validació

S'inclou l'element _resposta_

```xml
<xs:complexType name="resposta">
        <xs:sequence>
            <xs:element type="xs:string" name="codi" minOccurs="0"/>
            <xs:element type="xs:string" name="descripcio" minOccurs="0"/>
            <xs:element type="xs:string" name="tipusSolicitud" minOccurs="0"/>
        </xs:sequence>
</xs:complexType>
```

Camp | Descripció
---- | ----------
codi | Codi del resultat de la petició
descripcio | Descripció del resultat de la petició
tipusSolicitud | Tipus de sol·licitud de la petició

# 4. Exemples de peticions

## 4.1 Peticio de validació. 
En aquest exemple es pregunta si existeix alguna representació vàlida perquè el representant amb NIF 99999999A pugui actuar (en aquest cas amb la capacitat de CONSULTA) en nom del poderdant amb NIF 12345678Z per al tramit amb codi 1111 a l'administració amb codi 800180001.

```xml
<r:validarRepresentacio>
	<r:dades>
		<r:representacio>
			<r:poderdant>
				<r:tipusDocumentIdentificatiu>NIF</r:tipusDocumentIdentificatiu>
				<r:valorDocumentIdentificatiu>12345678Z</r:valorDocumentIdentificatiu>
				<r:tipusPersona>FISICA</r:tipusPersona>
			</r:poderdant>
			<r:representant>
				<r:tipusDocumentIdentificatiu>NIF</r:tipusDocumentIdentificatiu>
				<r:valorDocumentIdentificatiu>99999999A</r:valorDocumentIdentificatiu>
				<r:tipusPersona>FISICA</r:tipusPersona>
			</r:representant>
			<r:ambitRepresentacio>
				<r:administracio>
					<r:codi>800180001</r:codi>
				</r:administracio>
				<r:tramit>					
					<r:codi>1111</r:codi>
				</r:tramit>
				<r:capacitats>
					<r:capacitat>
						<r:codi>CONSULTA</r:codi>
					</r:capacitat>
				</r:capacitats>
			</r:ambitRepresentacio>
		</r:representacio>
		<r:dataValidacio>2019-03-07T00:00:00</r:dataValidacio>
		<r:funcionariSolicitant>
			<r:tipusDocumentIdentificatiu>NIF</r:tipusDocumentIdentificatiu>
			<r:valorDocumentIdentificatiu>99999999A</r:valorDocumentIdentificatiu>
			<r:tipusPersona>FISICA</r:tipusPersona>
		</r:funcionariSolicitant>
		<r:administracioSolicitant>
			<r:codi>1</r:codi>
		</r:administracioSolicitant>
	</r:dades>
</r:validarRepresentacio>
```

## 4.2 Resposta validació positiva
Si existeix una representació** que permet al representant actuar en nom del poderdant per aquest tramit, administració i capacitat, es retorna una resposta tipus:

```xml
<validarRepresentacioResponse xmlns="r:representa:V1.0">
 <resultat>
	<resposta>
	   <codi>0</codi>
	   <descripcio>S'ha validat positivament el poder sol·licitat</descripcio>
	   <tipusSolicitud>VALIDACIO</tipusSolicitud>
	</resposta>
	<representacio>
	   <identificadorLegal>201900000131</identificadorLegal>
	   <tipusRepresentacio>TIPUS_B</tipusRepresentacio>
	   <estat>VALIDA</estat>
	   <poderdant>
		  ...
	   </poderdant>
	   <representant>
		  ...
	   </representant>
	   <ambitRepresentacio>
		  <administracio>
			 <codi>800180001</codi>
			 <nif>P-0800100-J</nif>
			 <nom>Ajuntament d'Abrera</nom>
			 <activa>true</activa>
		  </administracio>
	   </ambitRepresentacio>
	   <dataCreacio>2019-03-08T00:00:00</dataCreacio>
	   <dataIniciVigencia>2019-03-05T00:00:00</dataIniciVigencia>
	   <dataFiVigencia>2019-03-08T00:00:00</dataFiVigencia>
	   <validacions>2</validacions>
	   <administracioReceptora>
		  <codi>9821920002</codi>
		  <nif>Q12345678</nif>
		  <nom>Consorci AOC</nom>
		  ...
	   </administracioReceptora>
	   <evidencies>
		  <evidencia>
			 <dataCreacio>2019-03-05T14:18:01</dataCreacio>
			 <identificadorLegal>201900000131</identificadorLegal>
			 <motiu>ALTA - Inscripció</motiu>
			 <administracioReceptora>
				<codi>9821920002</codi>
				<nif>Q12345678</nif>
				...
			 </administracioReceptora>
			 <funcionariReceptor>
				...
			 </funcionariReceptor>
			 <documentsEvidencia>
				<documentEvidencia>
				   ...
				</documentEvidencia>
			 </documentsEvidencia>
		  </evidencia>
	   </evidencies>
	</representacio>
 </resultat>
</validarRepresentacioResponse>
```

** _Només pot existir una representació vàlida que doni resposta positiva a una consulta de validació._

## 4.3 Resposta negativa
En cas que no existeixi cap representació que permeti respondre positivament a la consulta de validació, es retorna una resposta del tipus:

```xml
<validarRepresentacioResponse xmlns="r:representa:V1.0">
	<resultat>
        <resposta>
            <codi>001</codi>
            <descripcio>No hi ha cap apoderament inscrit amb aquesta informació</descripcio>
        </resposta>
    </resultat>
</validarRepresentacioResponse>
```

## 4.4 Petició de consulta de representació

```xml
<r:consultarRepresentacio>
	<r:ConsultaRepresentacio>            
		<r:identificadorLegal>201900000063</r:identificadorLegal>
	</r:ConsultaRepresentacio>
</r:consultarRepresentacio>
```

## 4.5 Resposta de consulta de representació

```xml
<consultarRepresentacioResponse xmlns="r:representa:V1.0">
	 <resultat>
		<resposta>
		   <codi>0</codi>
		   <descripcio>L'operació ha estat executada correctament</descripcio>
		   <tipusSolicitud>CONSULTA</tipusSolicitud>
		</resposta>
		<representacio>
		   <identificadorLegal>201900000063</identificadorLegal>
		   <tipusRepresentacio>TIPUS_C</tipusRepresentacio>
		   <estat>EXPIRADA</estat>
		   <poderdant>
			  ...
		   </poderdant>
		   <representant>
			  ...
		   </representant>
		   <ambitRepresentacio>
			  <administracio>
				 ...
			  </administracio>
			  <tramit>
				 ...
			  </tramit>
			  <capacitats>
				 ...
			  </capacitats>
		   </ambitRepresentacio>
		   <dataCreacio>2019-02-24T00:00:00</dataCreacio>
		   <dataIniciVigencia>2019-02-23T00:00:00</dataIniciVigencia>
		   <dataFiVigencia>2019-02-24T00:00:00</dataFiVigencia>
		   <validacions>0</validacions>
		   <administracioReceptora>
			  ...
		   </administracioReceptora>
		   <refAlta>a716be0b-4882-44a2-bd4a-7de30bbc8a8b-2512070005</refAlta>
		   <evidencies>
			  <evidencia>
				 ...
			  </evidencia>
			  <evidencia>
				 ...
			  </evidencia>
		   </evidencies>
		</representacio>
	</resultat>
 </consultarRepresentacioResponse>
```

# 5. Codis de resposta
_Pendent_
