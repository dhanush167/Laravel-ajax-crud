<p align="center"><img src="https://laravel.com/assets/img/components/logo-laravel.svg"></p>

<p align="center">
<a href="https://travis-ci.org/laravel/framework"><img src="https://travis-ci.org/laravel/framework.svg" alt="Build Status"></a>
<a href="https://packagist.org/packages/laravel/framework"><img src="https://poser.pugx.org/laravel/framework/d/total.svg" alt="Total Downloads"></a>
<a href="https://packagist.org/packages/laravel/framework"><img src="https://poser.pugx.org/laravel/framework/v/stable.svg" alt="Latest Stable Version"></a>
<a href="https://packagist.org/packages/laravel/framework"><img src="https://poser.pugx.org/laravel/framework/license.svg" alt="License"></a>
</p>

## About Laravel

Laravel is a web application framework with expressive, elegant syntax. We believe development must be an enjoyable and creative experience to be truly fulfilling. Laravel takes the pain out of development by easing common tasks used in many web projects, such as:

- [Simple, fast routing engine](https://laravel.com/docs/routing).
- [Powerful dependency injection container](https://laravel.com/docs/container).
- Multiple back-ends for [session](https://laravel.com/docs/session) and [cache](https://laravel.com/docs/cache) storage.
- Expressive, intuitive [database ORM](https://laravel.com/docs/eloquent).
- Database agnostic [schema migrations](https://laravel.com/docs/migrations).
- [Robust background job processing](https://laravel.com/docs/queues).
- [Real-time event broadcasting](https://laravel.com/docs/broadcasting).

Laravel is accessible, powerful, and provides tools required for large, robust applications.

## Learning Laravel

Laravel has the most extensive and thorough [documentation](https://laravel.com/docs) and video tutorial library of all modern web application frameworks, making it a breeze to get started with the framework.

If you don't feel like reading, [Laracasts](https://laracasts.com) can help. Laracasts contains over 1400 video tutorials on a range of topics including Laravel, modern PHP, unit testing, and JavaScript. Boost your skills by digging into our comprehensive video library.

## Laravel Sponsors

We would like to extend our thanks to the following sponsors for funding Laravel development. If you are interested in becoming a sponsor, please visit the Laravel [Patreon page](https://patreon.com/taylorotwell).



<h1>Ajax crud operations in Laravel 5.8 with Modal & Pagination</h1>


<h2>Install Yajra Datatable Package</h2>
We need to install yajra datatable composer package for datatable, so you can install using following command:


    composer require yajra/laravel-datatables-oracle

After that you need to set providers and alias.

    .....
    
    'providers' => [
    
    	....
    
    	Yajra\DataTables\DataTablesServiceProvider::class,
    
    ]
    
    'aliases' => [
    
    	....
    
    	'DataTables' => Yajra\DataTables\Facades\DataTables::class,
    
    ]
    
    .....


**Create Table**

    php artisan make:migration create_products_table --create=products

After this command you will find one file in following path "database/migrations" and you have to put bellow code in your migration file for create products table.


    <?php
    
    use Illuminate\Support\Facades\Schema;
    
    use Illuminate\Database\Schema\Blueprint;
    
    use Illuminate\Database\Migrations\Migration;
    
    class CreateProductsTable extends Migration
    
    {
    
        /**
    
         * Run the migrations.
    
         *
    
         * @return void
    
         */
    
        public function up()
    
        {
    
            Schema::create('products', function (Blueprint $table) {
    
                $table->increments('id');
    
                $table->string('name');
    
                $table->text('detail');
    
                $table->timestamps();
    
            });
    
        }
    
        /**
    
         * Reverse the migrations.
    
         *
    
         * @return void
    
         */
    
        public function down()
    
        {
    
            Schema::dropIfExists('products');
    
        }
    
    }

Now you have to run this migration by following command:

    php artisan migrate

**routes/web.php**

    Route::resource('ajaxproducts','ProductAjaxController');


**app/Http/Controllers/ProductAjaxController.php**

    <?php
    
    namespace App\Http\Controllers;
    
    use App\Product;
    
    use Illuminate\Http\Request;
    
    use DataTables;
    
    class ProductAjaxController extends Controller
    
    {
    
        /**
    
         * Display a listing of the resource.
    
         *
    
         * @return \Illuminate\Http\Response
    
         */
    
        public function index(Request $request)
    
        {
    
            if ($request->ajax()) {
    
                $data = Product::latest()->get();
    
                return Datatables::of($data)
    
                        ->addIndexColumn()
    
                        ->addColumn('action', function($row){
    
                               $btn = '<a href="javascript:void(0)" data-toggle="tooltip"  data-id="'.$row->id.'" data-original-title="Edit" class="edit btn btn-primary btn-sm editProduct">Edit</a>';
    
                               $btn = $btn.' <a href="javascript:void(0)" data-toggle="tooltip"  data-id="'.$row->id.'" data-original-title="Delete" class="btn btn-danger btn-sm deleteProduct">Delete</a>';
    
                                return $btn;
    
                        })
    
                        ->rawColumns(['action'])
    
                        ->make(true);
    
            }
    
            return view('productAjax',compact('products'));
    
        }
    
        /**
    
         * Store a newly created resource in storage.
    
         *
    
         * @param  \Illuminate\Http\Request  $request
    
         * @return \Illuminate\Http\Response
    
         */
    
        public function store(Request $request)
    
        {
    
            Product::updateOrCreate(['id' => $request->product_id],
    
                    ['name' => $request->name, 'detail' => $request->detail]);        
    
            return response()->json(['success'=>'Product saved successfully.']);
    
        }
    
        /**
    
         * Show the form for editing the specified resource.
    
         *
    
         * @param  \App\Product  $product
    
         * @return \Illuminate\Http\Response
    
         */
    
        public function edit($id)
    
        {
    
            $product = Product::find($id);
    
            return response()->json($product);
    
        }
    
        /**
    
         * Remove the specified resource from storage.
    
         *
    
         * @param  \App\Product  $product
    
         * @return \Illuminate\Http\Response
    
         */
    
        public function destroy($id)
    
        {
    
            Product::find($id)->delete();
    
            return response()->json(['success'=>'Product deleted successfully.']);
    
        }
    
    }


**app/Product.php**

    <?php
    
    namespace App;
    
    use Illuminate\Database\Eloquent\Model;
    
    class Product extends Model
    
    {
    
        protected $fillable = [
    
            'name', 'detail'
    
        ];
    
    }

**Create Blade Files**
**resources/views/productAjax.blade.php**


    <!DOCTYPE html>
    
    <html>
    
    <head>
    
        <title>Laravel 5.8 Ajax CRUD tutorial using Datatable - HDTuto.com</title>
    
        <meta name="csrf-token" content="{{ csrf_token() }}">
    
        <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/twitter-bootstrap/4.1.3/css/bootstrap.min.css" />
    
        <link href="https://cdn.datatables.net/1.10.16/css/jquery.dataTables.min.css" rel="stylesheet">
    
        <link href="https://cdn.datatables.net/1.10.19/css/dataTables.bootstrap4.min.css" rel="stylesheet">
    
        <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.js"></script>  
    
        <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery-validate/1.19.0/jquery.validate.js"></script>
    
        <script src="https://cdn.datatables.net/1.10.16/js/jquery.dataTables.min.js"></script>
    
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/js/bootstrap.min.js"></script>
    
        <script src="https://cdn.datatables.net/1.10.19/js/dataTables.bootstrap4.min.js"></script>
    
    </head>
    
    <body>
    
    <div class="container">
    
        <h1>Laravel 5.8 Ajax CRUD tutorial using Datatable - HDTuto.com</h1>
    
        <a class="btn btn-success" href="javascript:void(0)" id="createNewProduct"> Create New Product</a>
    
        <table class="table table-bordered data-table">
    
            <thead>
    
                <tr>
    
                    <th>No</th>
    
                    <th>Name</th>
    
                    <th>Details</th>
    
                    <th width="280px">Action</th>
    
                </tr>
    
            </thead>
    
            <tbody>
    
            </tbody>
    
        </table>
    
    </div>
    
    <div class="modal fade" id="ajaxModel" aria-hidden="true">
    
        <div class="modal-dialog">
    
            <div class="modal-content">
    
                <div class="modal-header">
    
                    <h4 class="modal-title" id="modelHeading"></h4>
    
                </div>
    
                <div class="modal-body">
    
                    <form id="productForm" name="productForm" class="form-horizontal">
    
                       <input type="hidden" name="product_id" id="product_id">
    
                        <div class="form-group">
    
                            <label for="name" class="col-sm-2 control-label">Name</label>
    
                            <div class="col-sm-12">
    
                                <input type="text" class="form-control" id="name" name="name" placeholder="Enter Name" value="" maxlength="50" required="">
    
                            </div>
    
                        </div>
    
                        <div class="form-group">
    
                            <label class="col-sm-2 control-label">Details</label>
    
                            <div class="col-sm-12">
    
                                <textarea id="detail" name="detail" required="" placeholder="Enter Details" class="form-control"></textarea>
    
                            </div>
    
                        </div>
    
                        <div class="col-sm-offset-2 col-sm-10">
    
                         <button type="submit" class="btn btn-primary" id="saveBtn" value="create">Save changes
    
                         </button>
    
                        </div>
    
                    </form>
    
                </div>
    
            </div>
    
        </div>
    
    </div>
    
    </body>
    
    <script type="text/javascript">
    
      $(function () {
    
          $.ajaxSetup({
    
              headers: {
    
                  'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
    
              }
    
        });
    
        var table = $('.data-table').DataTable({
    
            processing: true,
    
            serverSide: true,
    
            ajax: "{{ route('ajaxproducts.index') }}",
    
            columns: [
    
                {data: 'DT_RowIndex', name: 'DT_RowIndex'},
    
                {data: 'name', name: 'name'},
    
                {data: 'detail', name: 'detail'},
    
                {data: 'action', name: 'action', orderable: false, searchable: false},
    
            ]
    
        });
    
        $('#createNewProduct').click(function () {
    
            $('#saveBtn').val("create-product");
    
            $('#product_id').val('');
    
            $('#productForm').trigger("reset");
    
            $('#modelHeading').html("Create New Product");
    
            $('#ajaxModel').modal('show');
    
        });
    
        $('body').on('click', '.editProduct', function () {
    
          var product_id = $(this).data('id');
    
          $.get("{{ route('ajaxproducts.index') }}" +'/' + product_id +'/edit', function (data) {
    
              $('#modelHeading').html("Edit Product");
    
              $('#saveBtn').val("edit-user");
    
              $('#ajaxModel').modal('show');
    
              $('#product_id').val(data.id);
    
              $('#name').val(data.name);
    
              $('#detail').val(data.detail);
    
          })
    
       });
    
        $('#saveBtn').click(function (e) {
    
            e.preventDefault();
    
            $(this).html('Sending..');
    
            $.ajax({
    
              data: $('#productForm').serialize(),
    
              url: "{{ route('ajaxproducts.store') }}",
    
              type: "POST",
    
              dataType: 'json',
    
              success: function (data) {
    
                  $('#productForm').trigger("reset");
    
                  $('#ajaxModel').modal('hide');
    
                  table.draw();
    
              },
    
              error: function (data) {
    
                  console.log('Error:', data);
    
                  $('#saveBtn').html('Save Changes');
    
              }
    
          });
    
        });
    
        $('body').on('click', '.deleteProduct', function () {
    
            var product_id = $(this).data("id");
    
            confirm("Are You sure want to delete !");
    
            $.ajax({
    
                type: "DELETE",
    
                url: "{{ route('ajaxproducts.store') }}"+'/'+product_id,
    
                success: function (data) {
    
                    table.draw();
    
                },
    
                error: function (data) {
    
                    console.log('Error:', data);
    
                }
    
            });
    
        });
    
      });
    
    </script>
    
    </html>






























