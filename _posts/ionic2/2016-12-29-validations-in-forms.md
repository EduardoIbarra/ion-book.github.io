---
layout: post
title: "Validaciones en Formularios"
keywords: "validaciones con ionic, formularios con angular 2, validar email con angular 2, formbuilder, formularios con ionic 2"
date: 2017-01-03
tags: [forms, demos, ionic2]
categories: ionic2
laucher: "/launcher/demo109"
author: nicobytes
cover: "https://firebasestorage.googleapis.com/v0/b/ion-book.appspot.com/o/posts%2Fvalidations-in-forms%2FValidaciones%20en%20Formularios.jpg?alt=media"
remember: true
---

> La forma más común de capturar información de los usuarios son los **Formularios** y depende de una buena UI/UX ganar o perder un usuario en nuestra aplicación.
<!--summary-->

<amp-img width="1024" height="512" layout="responsive" src="https://firebasestorage.googleapis.com/v0/b/ion-book.appspot.com/o/posts%2Fvalidations-in-forms%2FValidaciones%20en%20Formularios.jpg?alt=media" alt="Validaciones en Formularios"></amp-img>

Por eso es de vital importancia hacer un buen manejo de ellos, tener las validaciones adecuadas y por esto Angular2 nos ofrece **FormBuilder**, una clase que nos provee una completa herramienta para controlar y validar formularios de forma muy eficiente y sencilla.

Antes puedes ver nuestro artículo anterior [**Forms con Ionic 2**]({{site.urlblog}}/ionic2/form-builder/){:target="_blank"} donde hacemos la construcción de un formulario sencillo, en este artículo nos enfocaremos en las **validaciones**.

En Angular 2 existen tres maneras de trabajar con formularios:

## Forms con NgModel

Es mucho más simple de trabajar y es muy familiar a Angular 1, pero se tiene menos control y es difícil hacer pruebas unitarias usando NgModel.

## Forms con Templates

De esta manera la lógica está casi toda en el template, lo que puede llegar a ser difícil de mantener, hacer test y el template de puede ver desordenado.

## Forms con FormBuilder

Con FormBuilder dejas toda la lógica del lado del controlador (esto es muy útil), es mucho más ordenado, fácil para hacer pruebas y fácil de mantener, lo unico es que debes agregar un import en tu controlador.

Para crear nuestro formulario con FormBuilder, vamos a ver la siguiente estructura básica:

{% highlight ts %}
this.myForm = this.fb.group({
  'name': ['', Validators.required],
  'company': ['', Validators.required],
  'email': ['', Validators.required],
  'age': ['', Validators.required],
  'url': ['', Validators.required],
  'password': ['', Validators.required],
});
{% endhighlight %}

La clase completa se vera asi `home.ts`:

{% highlight ts %}
import { Component } from '@angular/core';
import { FormBuilder, FormGroup, Validators} from '@angular/forms';
import { NavController } from 'ionic-angular';

@Component({
  selector: 'page-home',
  templateUrl: 'home.html'
})
export class HomePage {

  myForm: FormGroup;

  constructor(
    public navCtrl: NavController,
    public fb: FormBuilder
  ) {
    this.myForm = this.fb.group({
      'name': ['', Validators.required],
      'company': ['', Validators.required],
      'email': ['', Validators.required],
      'age': ['', Validators.required],
      'url': ['', Validators.required],
      'password': ['', Validators.required],
    });
  }

  saveData(){
    console.log(this.myForm.value);
  }

}
{% endhighlight %}

Con el método `saveData` obtenemos la información del formulario luego que todo sea validado.

Antes de empezar, es necesario entender los conceptos básicos para desarrollar el demo de este artículo.

Observamos que todos los campos tienen una validación la cual es `Validators.required`, es decir que todos los campos son obligatorios. Con Angular 2 y FormBuilder podemos agregar validaciones tan complejas como queramos, existen dos tipos de validaciones.

## Validaciones asíncronas

Las validaciones asíncronas son aquellas en las cuales debemos hacer una solicitud externa y de acuerdo a ello validar los datos, por ejemplo para validar un nombre de usuario (usersname), primero debemos hacer una solicitud a nuestra base de datos y comprobar que el nombre de usuario está disponible, esto es una validación asíncrona.

## Validaciones síncronas

La validación asíncrona es aquella que no necesitamos de consultar ninguna fuente externa para comprobar los datos, como por ejemplo validar que el email tiene el formato correcto ó validar que el usuario sea mayor un adulto a partir de la edad ó validar un número mínimo de caracteres etc.

Teniendo esto en cuenta podemos hacer combinaciones muy útiles de validaciones respetando esta la siguiente forma:

{% highlight ts %}
'field': [_value_, [validaciones sincronas], [validaciones asíncronas]],
{% endhighlight %}

Como ven primero va el valor por defecto, luego declaramos el conjunto de la validaciones sincronas y luego el conjunto de validaciones asíncronas. En este artículo trabajaremos con validaciones sincronas (más adelante escribiremos sobre las validaciones asíncronas).

**Angular 2**, son provee la clase `Validators` que trae funciones comunes que son de mucha utilidad en los formularios:

1. **Validators.required** = Comprueba que el campo sea llenado.
1. **Validators.minLength** = Comprueba que el campo cumpla con un mínimo de caracteres.
1. **Validators.maxLength** = Comprueba que el campo cumpla con un máximo de caracteres.
1. **Validators.pattern** = Comprueba que el campo cumpla con un patrón usando una expresión regular.

Con estas funciones podemos crear conjuntos de validaciones, por ejemplo al campo company podemos agregar el siguiente conjunto de validaciones:

{% highlight ts %}
'company': ['', [Validators.required, Validators.minLength(5), Validators.maxLength(10)]],
{% endhighlight %}

Y esto hará que el campo cumpla con todas las validaciones que fueron declaradas para que el campo `company` sea un campo válido.

Y esto hará que el campo cumpla con todas las validaciones que fueron declaradas para que el campo company sea un campo válido. También podemos crear nuestras propias validaciones, pero esto lo veremos en los próximos artículos.

Ahora nos enfocaremos en el template y mostrar los errores de cada campo al usuario, como ejemplo tomaremos en cuenta nuestro campo `company` que debe cumplir con varias validaciones al mismo tiempo.

{% highlight html %}
...
<ion-item>
  <ion-label stacked>Company:</ion-label>
  <ion-input formControlName="company" type="text" placeholder="company"></ion-input>
</ion-item>
<ion-item *ngIf="myForm.get('company').errors && myForm.get('company').dirty">
  <p *ngIf="myForm.get('company').hasError('required')">Field is required</p>
  <p *ngIf="myForm.get('company').hasError('minlength')">Min of 5 characters</p>
  <p *ngIf="myForm.get('company').hasError('maxlength')">Max of 10 characters</p>
</ion-item>
..
{% endhighlight %}

Como podemos ver se hace uso del elemento myForm dentro del formulario y así podemos obtener el estado actual del campo, y haciendo uso de `myForm.get('field').hasError(‘typeError')` podremos saber cuál mensaje mostrar al usuario dependiendo del error que tenga el campo.

Ahora si con estos conceptos claros podemos iniciar con nuestro demo (Demo Time).

## Paso 1: Iniciando el proyecto

Lo primero que haremos será iniciar un nuevo proyecto con ionic, si no lo recuerdas puedes ver esto con mas detalle en la [Introduccion a Ionic 2]({{site.urlblog}}/ionic2/ionic2){:target="_blank"}.
Vamos a nuestra terminal y ejecutamos:

```
ionic start demo109 blank --v2
```

Ahora entramos a la carpeta del proyecto desde nuestra terminal con:

```
cd demo109
```

Como iniciamos nuestro proyecto con el template **blank** tendremos una estructura básica del proyecto, la carpeta en la que vamos a trabajar sera *app*.

<div class="row">
  <div class="col col-100 col-md-50 col-lg-50">
    <amp-img width="500" height="493" layout="responsive" src="https://firebasestorage.googleapis.com/v0/b/ion-book.appspot.com/o/demos%2Fdemo102%2FScreenshot%20from%202016-11-06%2012-46-16.png?alt=media"></amp-img>
  </div>
</div>

Luego agregamos la plataforma para la que vamos a desarrollar:

```
ionic platform add android
ionic platform add ios
```

## Paso 2: Crear el Formulario

Vamos a usar FormBuilder para crear nuestro formulario, de esta manera:

{% highlight ts linenos %}
import { Component } from '@angular/core';
import { FormBuilder, FormGroup, Validators} from '@angular/forms';
import { NavController } from 'ionic-angular';

@Component({
  selector: 'page-home',
  templateUrl: 'home.html'
})
export class HomePage {

  myForm: FormGroup;

  constructor(
    public navCtrl: NavController,
    public fb: FormBuilder
  ) {
    this.myForm = this.fb.group({
      'name': ['', [Validators.required]],
      'company': ['', [Validators.required, Validators.minLength(5), Validators.maxLength(10)]],
      'email': ['', [Validators.required, Validators.pattern(/^([a-z0-9_\.-]+)@([\da-z\.-]+)\.([a-z\.]{2,6})$/)]],
      'age': ['', [Validators.required]],
      'url': ['', [Validators.pattern(/^(https?:\/\/)?([\da-z\.-]+)\.([a-z\.]{2,6})([\/\w \.-]*)*\/?$/)]],
      'password': ['', [Validators.pattern(/^[a-z0-9_-]{6,18}$/)]],
    });
  }

  saveData(){
    console.log(this.myForm.value);
  }

}
{% endhighlight %}

como podemos ver usamos todos los métodos que nos provee la clase `Validators`.

### Expresiones regulares

Las expresiones regulares son muy frecuente para validar datos, las expresiones y imagenes a continuacion son del artiulo [8 Regular Expressions You Should Know](https://code.tutsplus.com/tutorials/8-regular-expressions-you-should-know--net-6149){:target="_blank"}

En la línea 20 usamos la siguiente expresión regular para validar el **email**:

<div class="row">
  <div class="col col-100 col-md-66 col-lg-66">
    <amp-img width="600" height="500" layout="responsive" src="https://cdn.tutsplus.com/net/uploads/legacy/404_regularExpressions/images/email.jpg"></amp-img>
  </div>
</div>

```
/^([a-z0-9_\.-]+)@([\da-z\.-]+)\.([a-z\.]{2,6})$/
```

En la línea 22 usamos la siguiente expresión regular para validar una **url**:

<div class="row">
  <div class="col col-100 col-md-66 col-lg-66">
    <amp-img width="600" height="500" layout="responsive" src="https://cdn.tutsplus.com/net/uploads/legacy/404_regularExpressions/images/url.jpg"></amp-img>
  </div>
</div>

```
/^(https?:\/\/)?([\da-z\.-]+)\.([a-z\.]{2,6})([\/\w \.-]*)*\/?$/
```


En la línea 23 usamos la siguiente expresión regular para validar un **password**:

<div class="row">
  <div class="col col-100 col-md-66 col-lg-66">
    <amp-img width="600" height="370" layout="responsive" src="https://cdn.tutsplus.com/net/uploads/legacy/404_regularExpressions/images/password.jpg"></amp-img>
  </div>
</div>

```
/^[a-z0-9_-]{6,18}$/
```

## Paso 3: El template

Ahora en el template vamos a controlar cómo mostrar los errores al usuario a partir de el formulario `myForm` declarada en el controlador, nuestro template completo será el siguiente:

{% highlight html linenos %}
<form [formGroup]="myForm" (ngSubmit)="saveData()" novalidate>
  <ion-list> 
    <ion-item>
      <ion-label stacked>Name:</ion-label>
      <ion-input formControlName="name" type="text" placeholder="name"></ion-input>
    </ion-item>
    <ion-item *ngIf="myForm.get('name').errors && myForm.get('name').dirty">
      <p *ngIf="myForm.get('name').hasError('required')">Field is required</p>
    </ion-item>
    <ion-item>
      <ion-label stacked>Company:</ion-label>
      <ion-input formControlName="company" type="text" placeholder="company"></ion-input>
    </ion-item>
    <ion-item *ngIf="myForm.get('company').errors && myForm.get('company').dirty">
      <p *ngIf="myForm.get('company').hasError('required')">Field is required</p>
      <p *ngIf="myForm.get('company').hasError('minlength')">Min of 5 characters</p>
      <p *ngIf="myForm.get('company').hasError('maxlength')">Max of 10 characters</p>
    </ion-item>
    <ion-item>
      <ion-label stacked>E-mail:</ion-label>
      <ion-input formControlName="email" type="text" placeholder="email"></ion-input>
    </ion-item>
    <ion-item *ngIf="myForm.get('email').errors && myForm.get('email').dirty">
      <p *ngIf="myForm.get('email').hasError('required')">Field is required</p>
      <p *ngIf="myForm.get('email').hasError('pattern')">It is not an email</p>
    </ion-item>
    <ion-item>
      <ion-label stacked>Age:</ion-label>
      <ion-input formControlName="age" type="number" placeholder="age"></ion-input>
    </ion-item>
    <ion-item *ngIf="myForm.get('age').errors && myForm.get('age').dirty">
      <p *ngIf="myForm.get('age').hasError('required')">Field is required</p>
      <p *ngIf="myForm.get('age').hasError('invalid')">You are not adult</p>
    </ion-item>
    <ion-item>
      <ion-label stacked>Url:</ion-label>
      <ion-input formControlName="url" type="text" placeholder="url"></ion-input>
    </ion-item>
    <ion-item *ngIf="myForm.get('url').errors && myForm.get('url').dirty">
      <p *ngIf="myForm.get('url').hasError('required')">Field is required</p>
      <p *ngIf="myForm.get('url').hasError('pattern')">It is not an url</p>
    </ion-item>
    <ion-item>
      <ion-label stacked>Password:</ion-label>
      <ion-input formControlName="password" type="password" placeholder="password"></ion-input>
    </ion-item>
    <ion-item *ngIf="myForm.get('password').errors && myForm.get('password').dirty">
      <p *ngIf="myForm.get('password').hasError('required')">Field is required</p>
      <p *ngIf="myForm.get('password').hasError('pattern')">It is not a strong password</p>
    </ion-item>
  </ion-list>
  <div padding>
    <button ion-button block type="submit" [disabled]="myForm.invalid">Guardar</button>
  </div>
</form>
{% endhighlight %}

En el template agregamos cada uno de los mensajes correspondientes a cada error, finalmente si todo el formulario es válido se habilita el botón de guardar (línea 53) y podremos obtener la información con el método `saveData` que está en `home.ts` 