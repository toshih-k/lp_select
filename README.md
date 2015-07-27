# LpSelect


This gem provides both an FFI interface to the lp_solve library and a simplified interface
designed to pick from a set of choices to satisfy constraints (LPSelect). 

lp_solve is a Mixed Integer Linear Programming (MILP) solver.  It solves pure linear, (mixed) integer/binary, 
semi-cont and special ordered sets (SOS) models.  lp_solve is written in ANSI C and can be compiled on many 
different platforms like Linux and WINDOWS.

The lp_solve library is included and dynamically loaded.  The gem will look for the library in the 
lib/binaries folder, and searches for the first library it can load in this order:

liblpsolve55.dylib
liblpsolve55.dylib-ppc
liblpsolve55.dylib.x86-64
liblpsolve55.so
liblpsolve55.so-ux64
lpsolve55.dll


You can download and compile the liblpsolve library from http://sourceforge.net/projects/lpsolve/

LPSelect is designed for selecting a set of things that satisfies as many constraints as possible.
The lp_select_test.rb test is a contrived example showing how it can be used to select a fruit salad
at the lowest cost that satisfies the most people.  The price of each fruit is a weight and the objective
is to minimize the cost (ie weight) of the selected fruits.  Each person's choices is added as a
constraint row.


## Installation

Add this line to your application's Gemfile:

    gem 'lp_select'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install lp_select


## Usage

Current usage is focused on a handful of known problems to be solved, but it is easily extensible.  

The lp_solve library uses 1 indexed arrays.

There is more example usage in the LPSelect library and tests

```ruby
# Make a three row five column equation
@lp = LPSolve::make_lp(3, 5) 

# Set some column names
LPSolve::set_col_name(@lp, 1, "fred")
LPSolve::set_col_name(@lp, 2, "bob")
# Add a constraint and a row name, the API expects a 1 indexed array
constraint_vars = [0, 0, 1]
FFI::MemoryPointer.new(:double, constraint_vars.size) do |p|
  p.write_array_of_double(constraint_vars)
  LPSolve::add_constraint(@lp, p, LPSelect::EQ, 1.0.to_f)
end
LPSolve::set_row_name(@lp, 1, "onlyBob")

# Set the objective function and minimize it
constraint_vars = [1.0, 3.0]
FFI::MemoryPointer.new(:double, constraint_vars.size) do |p|
  p.write_array_of_double(constraint_vars)
  LPSolve::set_obj_fn(@lp, p)
end
LPSolve::set_minim(@lp)

# Solve it and retreive the result
LPSolve::solve(@lp) 
@objective = LPSolve::get_objective(@lp)

```


## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request


## Contributors
* James Prior
* Jake Sower
* Mattias Ekberg
