CLASS lhc_ZI_PRODUCTOS_LIMPIEZA DEFINITION INHERITING FROM cl_abap_behavior_handler.
  PRIVATE SECTION.

    METHODS validarStock FOR VALIDATE ON SAVE
      IMPORTING keys FOR zi_productos_limpieza~validarStock.

ENDCLASS.

CLASS lhc_ZI_PRODUCTOS_LIMPIEZA IMPLEMENTATION.

METHOD validarStock.

    READ ENTITIES OF zi_productos_limpieza IN LOCAL MODE
      ENTITY zi_productos_limpieza
        FIELDS ( Stock Nombre ) WITH CORRESPONDING #( keys )
      RESULT DATA(lt_productos).


    LOOP AT lt_productos INTO DATA(ls_producto).

      IF ls_producto-Stock <= 0.


        APPEND VALUE #( %tky = ls_producto-%tky ) TO failed-zi_productos_limpieza.


        APPEND VALUE #( %tky        = ls_producto-%tky
                        %state_area = 'VALIDAR_STOCK'
                        %msg        = new_message_with_text(
                                        severity = if_abap_behv_message=>severity-error
                                        text     = |El producto '{ ls_producto-Nombre }' no puede tener stock menor o igual a 0.|
                                      )
                        %element-stock = if_abap_behv=>mk-on
                      ) TO reported-zi_productos_limpieza.

      ENDIF.
    ENDLOOP.
  ENDMETHOD.

ENDCLASS.
