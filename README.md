import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Carrinho de Compras',
      theme: ThemeData(
        primarySwatch: Colors.blue,
        brightness: Brightness.dark,
      ),
      home: MyHomePage(),
    );
  }
}

class MyHomePage extends StatefulWidget {
  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  List<Product> _products = [
    Product(
      id: 'p1',
      title: 'Maçã',
      price: 2.99,
      imageUrl: 'https://link_para_imagem_macas.jpg',
    ),
    Product(
      id: 'p2',
      title: 'Banana',
      price: 1.99,
      imageUrl: 'https://link_para_imagem_bananas.jpg',
    ),
    // Adicione mais produtos aqui
  ];

  List<Product> _cart = [];

  void _addToCart(Product product) {
    setState(() {
      _cart.add(product);
    });
  }

  void _removeFromCart(Product product) {
    setState(() {
      _cart.remove(product);
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Carrinho de Compras'),
        actions: [
          IconButton(
            icon: Icon(Icons.shopping_cart),
            onPressed: () {
              Navigator.push(
                context,
                MaterialPageRoute(builder: (context) => CartScreen(_cart, _removeFromCart)),
              );
            },
          ),
        ],
      ),
      body: GridView.builder(
        padding: const EdgeInsets.all(10.0),
        itemCount: _products.length,
        itemBuilder: (ctx, i) => ProductItem(_products[i], _addToCart),
        gridDelegate: SliverGridDelegateWithFixedCrossAxisCount(
          crossAxisCount: 2,
          childAspectRatio: 3 / 2,
          crossAxisSpacing: 10,
          mainAxisSpacing: 10,
        ),
      ),
    );
  }
}

class ProductItem extends StatelessWidget {
  final Product product;
  final Function _addToCart;

  ProductItem(this.product, this._addToCart);

  @override
  Widget build(BuildContext context) {
    return GridTile(
      child: Image.network(product.imageUrl, fit: BoxFit.cover),
      footer: GridTileBar(
        backgroundColor: Colors.black87,
        title: Text(product.title, textAlign: TextAlign.center),
        trailing: IconButton(
          icon: Icon(Icons.shopping_cart),
          onPressed: () {
            _addToCart(product);
          },
        ),
      ),
    );
  }
}

class Product {
  final String id;
  final String title;
  final double price;
  final String imageUrl;

  Product({
    required this.id,
    required this.title,
    required this.price,
    required this.imageUrl,
  });
}

class CartScreen extends StatelessWidget {
  final List<Product> _cart;
  final Function _removeFromCart;

  CartScreen(this._cart, this._removeFromCart);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Carrinho de Compras'),
      ),
      body: ListView.builder(
        itemCount: _cart.length,
        itemBuilder: (ctx, i) => ListTile(
          leading: CircleAvatar(
            backgroundImage: NetworkImage(_cart[i].imageUrl),
          ),
          title: Text(_cart[i].title),
          subtitle: Text('Preço: ${_cart[i].price}'),
          trailing: IconButton(
            icon: Icon(Icons.remove),
            onPressed: () {
              _removeFromCart(_cart[i]);
              Navigator.pop(context);
              Navigator.push(
                context,
                MaterialPageRoute(builder: (context) => CartScreen(_cart, _removeFromCart)),
              );
            },
          ),
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () {
          Navigator.push(
            context,
            MaterialPageRoute(builder: (context) => CheckoutScreen(_cart)),
          );
        },
 
      ),
    );
  }
}

class CheckoutScreen extends StatelessWidget {
  final List<Product> _cart;

  CheckoutScreen(this._cart);

  @override
  Widget build(BuildContext context) {
    double totalPrice = _cart.fold(0, (sum, product) => sum + product.price);

    return Scaffold(
      appBar: AppBar(
        title: Text('Checkout'),
      ),
      body: Padding(
        padding: const EdgeInsets.all(20.0),
        child: Column(
          children: [
            Text('Resumo do Pedido'),
            SizedBox(height: 20),
                        ListView.builder(
              shrinkWrap: true,
              physics: ClampingScrollPhysics(),
              itemCount: _cart.length,
              itemBuilder: (ctx, i) => ListTile(
                leading: CircleAvatar(
                  backgroundImage: NetworkImage(_cart[i].imageUrl),
                ),
                title: Text(_cart[i].title),
                subtitle: Text('Preço: ${_cart[i].price}'),
              ),
            ),
            SizedBox(height: 20),
            Text('Total: R\$ ${totalPrice.toStringAsFixed(2)}'),
            SizedBox(height: 20),
            ElevatedButton(
              onPressed: () {
                // Implemente a lógica de pagamento aqui
                print('Pagamento realizado com sucesso!');
              },
              child: Text('Realizar Pagamento'),
            ),
          ],
        ),
      ),
    );
  }
}
