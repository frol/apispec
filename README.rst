*******
apispec
*******

.. image:: https://travis-ci.org/marshmallow-code/apispec.svg?branch=dev
    :target: https://travis-ci.org/marshmallow-code/apispec

A pluggable API documentation generator. Currently supports the `Swagger 2.0 specification <http://swagger.io/specification/>`_.

Features
========

- Supports Swagger 2.0
- Framework-agnostic
- Includes plugins for marshmallow and Flask
- Utilities for parsing docstrings

Example Application
===================

.. code-block:: python

    from apispec import APISpec
    from flask import Flask, jsonify
    from marshmallow import Schema, fields

    # Create an APISpec
    spec = APISpec(
        title='Swagger Petstore',
        version='1.0.0',
        description='A sample Petstore API.',
        plugins=[
            'apispec.ext.flask',
            'apispec.ext.marshmallow',
        ],
    )

    # Optional marshmallow support
    class CategorySchema(Schema):
        id = fields.Int()
        name = fields.Str(required=True)

    class PetSchema(Schema):
        category = fields.Nested(CategorySchema, many=True)
        name = fields.Str()

    # Optional Flask support
    app = Flask(__name__)

    @app.route('/random')
    def random_pet():
        """A cute furry animal endpoint.
        ---
        get:
            description: Get a random pet
            responses:
                200:
                    description: A pet to be returned
                    schema: PetSchema
        """
        pet = get_random_pet()
        return jsonify(PetSchema().dump(pet).data)

    ctx = app.test_request_context()
    ctx.push()

    # Register entities and paths
    spec.definition('Category', schema=CategorySchema)
    spec.definition('Pet', schema=PetSchema)
    spec.add_path(view=random_pet)


Generated Swagger Spec
======================

.. code-block:: python

    spec.to_dict()
    # {
    #   "info": {
    #     "title": "Swagger Petstore",
    #     "version": "1.0.0"
    #   },
    #   "swagger": "2.0",
    #   "description": "A sample Petstore API."
    #   "paths": {
    #     "/random": {
    #       "get": {
    #         "description": "A cute furry animal endpoint."
    #         "responses": {
    #           "200": {
    #             "schema": {
    #               "$ref": "#/definitions/Pet"
    #             },
    #             "description": "A pet to be returned"
    #           }
    #         },
    #       }
    #     }
    #   },
    #   "definitions": {
    #     "Pet": {
    #       "properties": {
    #         "category": {
    #           "type": "array",
    #           "items": {
    #             "$ref": "#/definitions/Category"
    #           }
    #         },
    #         "name": {
    #           "type": "string"
    #         }
    #       }
    #     },
    #     "Category": {
    #       "required": [
    #         "name"
    #       ],
    #       "properties": {
    #         "name": {
    #           "type": "string"
    #         },
    #         "id": {
    #           "type": "integer",
    #           "format": "int32"
    #         }
    #       }
    #     }
    #   },
    # }


Documentation
-------------

Documentation is available at http://apispec.readthedocs.org/ .

License
=======

MIT licensed. See the bundled `LICENSE <https://github.com/marshmallow-code/apispec/blob/master/LICENSE>`_ file for more details.
