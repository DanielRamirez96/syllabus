# Expresiones regulares

### Motivaci�n

�C�mo escribir�an una funci�n que reciba un string y verifique si corresponde a un email de la PUC? Suponiendo que en la parte local solo puede haber letras, d�gitos, y el caracter "_"

```python
def is_mail_uc(string):
	if not string.count("@") == 1:
	    return False
    local, domain = string.split("@")
    if domain is not "uc.cl" and domain is not "puc.cl":
        return False
    if not local:
        return False
    valids = [i for i in range(ord('a'), ord('z')+1)]
    valids += [i for i in range(ord('A'), ord('Z')+1)]
    valids.append(ord('_'))
    for c in local:
        if ord(c) not in valids:
            return False
    return True
```

�C�mo la modificar�an si hubiera que permitir adem�s, correos con dominio `ing.pug.cl`, `mat.puc.cl`, `ing.uc.cl` o `mat.uc.cl`? **ARGH!** As� es como **NO** se hace esta funci�n.

### El m�dulo `re`

La misma funci�n (incluyendo mails de ingenier�a y matem�tica) puede escribirse como:
```python
import re


def is_mail_uc(string):
    pattern = "[a-zA-Z0-9_]+@((ing|mat)\.)?p?uc.cl"
    return bool(re.fullmatch(pattern, string))
```

�Y funciona!

### �Qu� es esto? �Magia negra?

Algo as�... las expresiones regulares son patrones de strings especialmente �tiles para:
* Ver si otro string cumple el patr�n (`fullmatch`)
* Ver si alg�n sub-string lo cumple (`search`)
* Reemplazar el patr�n por otra secuencia de caracteres (`sub`)
* Separar el string de acuerdo al patr�n (`split`)
* En general, jugar con patrones de strings!

### �C�mo funciona?

La sintaxis de los patrones se basa en caracteres especiales.
Por ejemplo,
* `"["` y `"]"`. Sirven para indicar distintos caracteres posibles en esa posici�n. Por ejemplo, `"a[bcde]f"` es un patr�n que representa a los strings: `"abf"`, `"acf"`, `"adf"`, `"aef"`.
* `"-"` sirve para indicar *rangos* de caracteres. El patr�n del ejemplo anterior tambi�n pod�a escribirse como `"a[b-e]f"`.
* `"+"` indica que la expresi�n anterior (ojo, esta puede contener varios caracteres posibles internamente) estar� presente una o m�s veces. As�, `"a[b-e]+f"` hace referencia a los mismos strings del primer ejemplo, pero tambi�n a `"abbf"`, `"abcf"`, `"abdedeeebdef"`, etc.
* `"*"` es similar a `"+"`, pero permite que la expresi�n previa no est� ninguna vez. `"a[b-e]*f"` permite, adem�s de los strings anteriores, al string `"af"`.
* `"|"` es para separar dos secuencias, y buscar� que en dicho lugar, haya una o la otra. Por ejemplo, `"abc|def"` representa a los strings `"abc"` o `"def"`, y `"(abc|def)+"`, tambi�n permite `"abcabcabc"` o `"abcabcdefabcabcdefdef"`.
* `"?"` indica que la expresi�n anterior puede estar una vez, o no estar.
* `"."` representa cualquier caracter, excepto un salto de l�nea.
* `"("` y `")"` funcionan como delimitadores.
* `"\"`. Como es usual en python, se usa para evitar que los caracteres de arriba se traten como especiales. Por ejemplo, si se desea escribir un patr�n para los strings `""`, `"*"`, `"**"`, `"***"`, ..., se puede hacer con `"\**"`.

### Entendiendo la magia...
Entonces, `"[a-zA-Z0-9_]+@((ing|mat)\.)?p?uc.cl"` est� diciendo que:
* `"[a-zA-Z0-9_]"` representa a una letra (tanto may�sculas como min�sculas, sin considerar la �), un d�gito del 0 al 9, o el caracter "_"
* Estos caracteres pueden aparecer una o m�s veces, pues est�n seguidos de `"+"`
* Luego, debe estar el caracter "@"
* Despu�s, `"(ing|mat)\."` representa a las secuencias `"ing"` o `"mat"`, seguidas de un punto. Esto puede estar o no, ya que est� seguida de `"?"`
* Puede o no estar el caracter `"p"`
* Debe terminar con `"uc.cl"`

### Conclusi�n (?)
* �No m�s funciones "a la antigua" para verificar cumplimiento de patrones en strings!
* Las expresiones regulares proporcionan una forma **optimizada**, **simple** y **comprensible** de hacer el mismo trabajo.
* �Hay varios otros caracteres especiales y funciones que pueden usar! Busquen en internet dependiendo de lo que quieran hacer.
