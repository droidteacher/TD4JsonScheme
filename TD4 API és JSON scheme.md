# TD4 API és JSON scheme

A TD4 mobil alkalmazás a szerverrel (backend) való kommunikációja során JSON üzeneteket fogad. A JSON üzenetek strutúrája kötött. Amennyiben a szerver az elvárt sémának nem megfelelő üzenetet küld egy adott http kérésre válaszul, akkor a mobil alkalmazás nagy valószínűséggel összeomlik ("*crash-el*").

## A TD4 által használt API végpontok

A mobilalkalmazás a következő végpontokra küld http GET kéréseket:

* *driver_app/getDriverState:* az alkalmazás teljes flow-ja általánosan használja a különböző user interakciók (gomb klikkelés) felküldésére. A http válaszban az alkalmazás megkapja az aktuális state-et, ami leírja a user interface szerver által elvárt állapotát
* *driver_app/gps:* a mobil alkalmazás ide küldi fel az Android eszköz aktuális GPS pozicióját
* *driver_app/bluetooth:* a mobil alkalmazás itt jelez vissza a szervernek, hogy kiküldött egy konkrét Bluetooth parancsot a taximéter felé

## A TD4 által elvárt JSON struktúrák

### Pre-login response

A mobilalkalmazás egy ún. "pre-login" kérést küld el legelőször a szerver felé. Az erre érkezett http válaszból építi fel a bejelentkezési képernyőt.

#### Elvárt JSON séma a http válaszban

A felkiáltójelek azt jelzik, hogy a paraméter **kötelező**.

A kérdőjelek azt jelzik, hogy a paraméter **opcionális** (elhagyható).

	{
		next_layout: String!,	
		next_state: String!,		
	
		button1: {
			enabled: Boolean!,
			visible: Boolean!,
			name: String!, 
			value: String!
			
			taximeter_commands: [
				{
					taximeter_command_id: Integer!,
					taximeter_command: String!,
					taximeter_parameter: String!
				}: Object!,
				{}: Object!,
				{}: Object!,...
			]: Array!
		}: Object!,
	
		edit1: {
			enabled: Boolean!,
			visible: Boolean!,
			name: String!, 
			value: String!,
			type: String?
		}: Object!,
		
		edit2: {
			enabled: Boolean!,
			visible: Boolean!,
			name: String!, 
			value: String!,
			type: String?
		}: Object!,
		
		edit3: {
			enabled: Boolean!,
			visible: Boolean!,
			name: String!, 
			value: String!,
			type: String?
		}: Object!,
	
		select1: {
			enabled: Boolean!,
			visible: Boolean!,
			value: String!,
			options: [
				{
					id: Integer!,
	    			licencePlate: String!,
	    			lastknownKm: String!,
	    			taximeter_bluetooth_name: String?,
	    			taximeter_id: String?
				}: Object!,
				{}: Object!,
				{}: Object!,...
			]: Array!
		
		}: Object!,
	
		session_id: String!
	}: Object!


#### Kotlin modell osztályok, amelyekre a válasz JSON konvertálódik

##### PreLoginResponse

	data class PreLoginResponse(
    	@SerializedName("next_layout") val nextLayout: String,
   
    	@SerializedName("next_state") val nextState: String,

    	val button1: TDMButton,
    	val edit1: TDMEdit,
    	val edit2: TDMEdit,
    	val edit3: TDMEdit,
    	val select1: TDMSelect,

    	@SerializedName("session_id") val sessionId: String
	)

##### TDMButton

	data class TDMButton(
    	val enabled: Boolean,
    	val visible: Boolean,
    	val name: String,
    	val value: String,

    	@SerializedName("taximeter_commands") val tmCommands: List<TDMTaximeterCommand>? = null
	): Serializable


##### TDMEdit

	data class TDMEdit(
    	val enabled: Boolean,
    	val visible: Boolean,
    	val name: String,
    	val value: String,
    	val type: String?,

    	@SerializedName("alt") val hint: String?
	): Serializable
	
	
	
##### TDMTaximeterCommand

	data class TDMTaximeterCommand(
		@SerializedName("taximeter_command_id") val id: Int,

    	@SerializedName("taximeter_command") val name: String,

    	@SerializedName("taximeter_parameter") val param: String
	): Serializable



##### TDMSelect

	data class TDMSelect(
		val enabled: Boolean, 
		val visible: Boolean, 
		val value: String, 
		val options: List<Car>
	)
	
##### Car
	
	data class Car(
    	val id: Int,
    	val licencePlate: String,
    	val lastknownKm: String,

    	@SerializedName("taximeter_bluetooth_name") val taximeterName: String? = null,

    	@SerializedName("taximeter_id") val taximeterId: String? = null
	): Serializable
	
	
---
	
### Generic response (általános státusz update a szervertől)

*!!! MÉG KIDOLGOZÁS ALATT !!!*

---

### GPS response (a felküldött GPS kordinátákra a szerver válasza)

#### Elvárt JSON séma a http válaszban

A felkiáltójelek azt jelzik, hogy a paraméter **kötelező**.

A kérdőjelek azt jelzik, hogy a paraméter **opcionális** (elhagyható).

	{
		result: Boolean!,
		new_request: Boolean!
	}: Object!

#### Kotlin modell osztályok, amelyekre a válasz JSON konvertálódik

##### GpsResponse

	data class GpsResponse(
	    val result: Boolean,
	    @SerializedName("new_request") val newRequest: Boolean
	)
	
---