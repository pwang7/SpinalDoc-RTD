
Formal
==========

General
-------

There is limited support for SystemVerilog Assertions (SVA).

You can add formal statements (assume, assert, etc.) in the ``Component`` definition, like in the example below:

.. code-block:: scala
 
    class TopLevel extends Component {
      val io = new Bundle {
        val ready = in Bool()
        val valid = out Bool()
      }
      val valid = RegInit(False)

     when(io.ready) {
       valid := False
     }
     io.valid <> valid
     // some logic

     import spinal.core.GenerationFlags._
     import spinal.core.Formal._

     GenerationFlags.formal {
       when(initstate()) {
         assume(clockDomain.isResetActive)
         assume(io.ready === False)
       }.otherwise {
         assert(!(valid.fall && !io.ready))
       }
     }
    }

To generate a design which includes the formal statements you can use ``includeFormal``:

.. code-block:: scala

   object MyToplevelSystemVerilogWithFormal {
    def main(args: Array[String]) {
      val config = SpinalConfig(defaultConfigForClockDomains = ClockDomainConfig(resetKind=SYNC, resetActiveLevel=HIGH))
       config.includeFormal.generateSystemVerilog(new TopLevel())
     }
   }


Clocked assertion without reset 
-----------------------------------

.. code-block:: scala

   ClockDomain.current.withoutReset(){
     assert(wuff === 0)
   }

Specifying the initial value of a signal 
-------------------------------------------

For instance, for the reset signal of the current clockdomain (usefull at the top)

.. code-block:: scala

    ClockDomain.current.readResetWire initial(False)

Specifying a initial assumption
-------------------------------------------

.. code-block:: scala

    assumeInitial(!clockDomain.isResetActive)

Specifying assertion in the reset scope
-------------------------------------------

.. code-block:: scala

    ClockDomain.current.duringReset {
      assume(rawrrr === 0)
      assume(wuff === 3)
    }

Supported features
------------------
 .. list-table::
    :header-rows: 1
    :widths: 3 1 3

    * - Syntax
      - Returns
      - Creates in SystemVerilog
    * - ``assert()``
      -
      - ``assert()``
    * - ``cover()``
      -
      - ``cover()``
    * - | ``past(that : T, delay : Int)``
        | ``past(that : T)``
      - T
      - ``past(that)``
    * - ``rose(that : Bool)``
      - Bool
      - ``rose(that)``
    * - ``fell(that : Bool)``
      - Bool
      - fell(that)
    * - ``changed(that : Bool)``
      - Bool
      - ``changed(that)``
    * - ``stable(that : Bool)``
      - Bool
      - ``stable(that)``
    * - ``initstate()``
      - Bool
      - ``$initstate()``

Limitations
-----------
No support for unclocked assertions.

