# Framework de Testing en Ruby

Desarrollo de un Framework de Testing, realizado como trabajo practico de la materia TADP de la UTN FRBA, con el fin de aprender conceptos de Metaprogramación.

## Features
- Unit Testing
- Mocks
- Spies

## Uso basico

```ruby
# Crear suite de testeo
# Una suite es toda clase que tenga al menos un metodo test 
# Un test es un metodo cuyo nombre empieza con el prefijo "testear_que_"

class MiSuiteDeTests
  def testear_que_pasa_algo
    # aserciones
  end

  def otro_metodo_que_no_es_un_test
    # ...
  end
end

# Dentro de un test, se pueden realizar aserciones utilizando el metodo "deberia" en cualquier objeto,
# pasando como parametro el tipo de asercion, y el valor de comparacion

# ejemplos:

class Persona
  def viejo?
    @edad > 29
  end
end

7.deberia ser 7 # pasa

true.deberia ser false # falla, obvio

leandro.edad.deberia ser 25 #falla (lean tiene 22)

leandro.edad.deberia ser mayor_a 20
# pasa si el objeto es mayor al parámetro

leandro.edad.deberia ser menor_a 25
# pasa si el objeto es menor al parámetro

leandro.edad.deberia ser uno_de_estos [7, 22, "hola"]
# pasa si el objeto está en la lista recibida por parámetro

leandro.edad.deberia ser uno_de_estos 7, 22, "hola"
# debe poder escribirse usando varargs en lugar de un array

# se puede utilizar "ser_" seguido del nombre de un metodo booleano
nico.deberia ser_viejo    # pasa: Nico tiene edad 30.
nico.viejo?.deberia ser true # La linea de arriba es equivalente a esta

leandro.deberia ser_viejo # falla: Leandro tiene 22.

leandro.deberia ser_joven # explota: Leandro no entiende el mensaje joven?´’

# objeto.deberia tener_<<nombre del atributo>> valor_esperado

leandro.deberia tener_edad 22 # pasa
leandro.deberia tener_nombre "leandro" # falla: no hay atributo nombre
leandro.deberia tener_nombre nil # pasa
leandro.deberia tener_edad mayor_a 20 # pasa
leandro.deberia tener_edad menor_a 25 # pasa
leandro.deberia tener_edad uno_de_estos [7, 22, "hola"] # pasa

leandro.deberia entender :viejo? # pasa
leandro.deberia entender :class  # pasa: este mensaje se hereda de Object
leandro.deberia entender :nombre # falla: leandro no entiende el mensaje

# Bloques de codigo:

en { 7 / 0 }.deberia explotar_con ZeroDivisionError # pasa
en { leandro.nombre }.deberia explotar_con NoMethodError # pasa
en { leandro.nombre }.deberia explotar_con Error # pasa: NoMethodError < Error
en { leandro.viejo?}.deberia explotar_con NoMethodError # falla: No tira error
en { 7 / 0 }.deberia explotar_con NoMethodError # falla: Tira otro error

# Mocking

class PersonaHome
  def todas_las_personas
    # Este método consume un servicio web que consulta una base de datos
  end

  def personas_viejas
    self.todas_las_personas.select{|p| p.viejo?}
  end
end

class PersonaHomeTests
  def testear_que_personas_viejas_trae_solo_a_los_viejos
    nico = Persona.new(30)
    axel = Persona.new(30)
    lean = Persona.new(22)

    # Mockeo el mensaje para no consumir el servicio y simplificar el test
    PersonaHome.mockear(:todas_las_personas) do
      [nico, axel, lean]
    end

    viejos = PersonaHome.new.personas_viejas

    viejos.deberia ser [nico, axel]
  end
end

# Spies

class PersonaTest

  def testear_que_se_use_la_edad
    lean = Persona.new(22)
    pato = Persona.new(23)
    pato = espiar(pato)

    pato.viejo?

    pato.deberia haber_recibido(:edad)
    # pasa: edad se llama durante la ejecución de viejo?

    pato.deberia haber_recibido(:edad).veces(1)
    # pasa: edad se recibió exactamente 1 vez.
    pato.deberia haber_recibido(:edad).veces(5)
    # falla: edad sólo se recibió una vez.

    pato.deberia haber_recibido(:viejo?).con_argumentos(19, "hola")
    # falla, recibió el mensaje, pero sin esos argumentos.

    pato.deberia haber_recibido(:viejo?).con_argumentos()
    # pasa, recibió el mensaje sin argumentos.

    lean.viejo?
    lean.deberia haber_recibido(:edad)
    # falla: lean no fue espiado!
end

```

## Enunciado
[TADsPec](https://docs.google.com/document/d/1-ph4ETb20CxWG3V-jXf2Iwufx0psFiK4aRTIBdqc6Gc/edit?tab=t.0)
