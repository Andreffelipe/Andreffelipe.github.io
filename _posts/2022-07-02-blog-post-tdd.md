## Vamos falar sobre TDD

### o que é o sigla TDD

TDD é uma sigla para Test Driven Development, o TDD e uma metodologia de desenvolvimento que nos auxiliar no desenvolvimento de software.

### Qual seu ciclo

No TDD temos 3 siclos
* 1 - Escrever um teste que falhe.
* 2 - Implemente o mínimo de código para satisfazer o teste.
* 3 - Refatore o código deixando-o mais legível e organizado.

### Projeto de exemplo

O projeto final se encontra nesse [link](https://gitlab.com/Andreffelipe/tdd)

vamos fazer uma carteira de bitcoins.

requisitos do projeto

* deve poder adicionar bitcoin na carteira
* deve poder sacar bitcoin se tiver saldo suficiente


vamos começar fazendo nosso primeiro teste

```js
// __test__/example.spec.ts

describe('BitcoinWallet', () => {
    const wallet = new BitcoinWallet()

    it('should be able to add 1000 bitcoin balance to wallet', () => {
        wallet.addBalance(1000)
        expect(wallet.getBalance()).toEqual(1000)
    });
});
```
no teste queremos adicionar 1000 bitcoins em nossa carteira, ao rodar os teste ele falhara
pois a `class BitcoinWallet` não existe nem seus métodos `addBalance` e `getBalance`,

<br/>
<img src="../_img/post_tdd/2022-02-07 19-22-46.png">
<br/><br/>

vamos cria-lo e rodar os teste outra vez,

```js
//src/bitcoinWallet.js
module.exports = class BitcoinWallet {
    addBalance(amount) {}
    getBalance() {}
}

```
ao cria nossa `class BitcoinWallet` e rodar os teste recebemos o seguinte error.

<img src="../_img/post_tdd/2022-02-07 19-33-26.png"/>
<br/><br/>
seguindo o primeiro passos do TDD, escreva o mínimo de código para passar o teste,

```js
//src/bitcoinWallet.js
module.exports = class BitcoinWallet {
    addBalance(amount) {}
    getBalance() {
        return 1000;
    }
}

```
ao rodar o teste ele passará,


<img src="../_img/post_tdd/2022-02-07 19-43-31.png"/>
<br/><br/>

lindo não!!!

Vamos cria mais dois teste , adicionar mais saldo em nossa carteira 

```js
// __test__/example.spec.ts

describe('BitcoinWallet', () => {
    const wallet = new BitcoinWallet()

    it('should be able to add 1000 bitcoin balance to wallet', () => {
        wallet.addBalance(1000)
        expect(wallet.getBalance()).toEqual(1000)
    });

    it('should be able to add 500 bitcoin balance to wallet', () => {
        wallet.addBalance(500)
        expect(wallet.getBalance()).toEqual(1500)
    });

    it('should return error when the value to be added is less than 1', () => {
        const error = wallet.addBalance(0)
        expect(error).toBeInstanceOf(Error)
    });
});

```

os teste falharam pois nosso método `getBalance` sempre retorna um valor fixo, e o outro teste falhara pois não validamos que para que aja um deposito tem que passar um valor mínimo de 1 bitcoin

<img src="../_img/post_tdd/2022-02-07 20-45-02.png"/>
<br/><br/>

bora resolver esses problemas

```js
//src/bitcoinWallet.js
module.exports = class BitcoinWallet {
    balance = 0
    addBalance(amount) {
        if (amount < 1) {
            return new Error("minimum value 1 bitcoin")
        }
        this.balance += amount;
    }
    getBalance() {
        return this.balance;
    }
}

```

criamos uma variável `balance` para guardar nosso saldo, quando chamamos o `addBalance` ele checa se o valor e menor que o mínimo para se depositar, se for retorna um erro se não adiciona o valor em nossa carteira, o `getBalance` agora retorna nosso saldo real.<br/><br/>
Vamos fazer saques?

Na nossa suite de teste vamos cria mais dois teste uma para sacar bitcoin e outro para validar se tem saldo suficiente para retirada,

```js
// __test__/example.spec.ts

describe('BitcoinWallet', () => {
    const wallet = new BitcoinWallet()

    it('should be able to add 1000 bitcoin balance to wallet', () => {
        wallet.addBalance(1000)
        expect(wallet.getBalance()).toEqual(1000)
    });

    it('should be able to add 500 bitcoin balance to wallet', () => {
        wallet.addBalance(500)
        expect(wallet.getBalance()).toEqual(1500)
    });

    it('should return error when the value to be added is less than 1', () => {
        const error = wallet.addBalance(0)
        expect(error).toBeInstanceOf(Error)
    });

    it('should be able to withdraw 1300 bitcoins', () => {
        wallet.withdraw(1300)
        expect(wallet.getBalance()).toEqual(200)
    });

    it('should return error for insufficient balance', () => {
        const error = wallet.withdraw(500)
        expect(error).toBeInstanceOf(Error)
    });
});

```
ao rodar os teste eles falaram

<img src="../_img/post_tdd/2022-02-07 21-14-30.png"/>
<br/><br/>
vamos refatora nosso código para que eles passem

```js
//src/bitcoinWallet.js
module.exports = class BitcoinWallet {
       balance = 0
    addBalance(amount) {
        if (amount < 1) {
            return new Error("minimum value 1 bitcoin")
        }
        this.balance += amount;
    }
    getBalance() {
        return this.balance;
    }
    withdraw(amount) {
        if (this.balance < amount) {
            return new Error("insufficient balance")
        }
        this.balance -= amount;
    }
}
```

com isso validamos se tem o saldo para o saque e debitamos da conta o valor que queríamos.

até uma proxima.

