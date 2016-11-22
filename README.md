# Sequelize to Joi

Takes a Sequelize model and constructs a Joi validator from it

## Usage

```
sequelizeToJoi(model, [options = {}])
```

### Example:

```
import sequelizeToJoi from '@revolttv/sequelize-to-joi';

let model = sequelize.define('YourModel', {
    name: Sequelize.STRING,
    json: {
        type: Sequelize.JSONB,
        sequelizeToJoiOverride: Joi.object({
            here: Joi.string().valid('you','can','override','autogenerated','validators');
        }),
    },
});

let validator = sequelizeToJoi(model);

Joi.validate({
        id: 1,
        name: 'wow',
        nope: 'failure',
        createdAt: '2016-07-22T12:00:00.000Z',
        updatedAt: '2016-07-22T12:00:00.000Z'
    },
    validator,
    (err, result) => {
        console.log(err); // will error out about `nope` key
    }
)

let validator2 = sequelizeToJoi(model, { omitAutoGenerated: true });
Joi.validate({ name: 'wow' , json: { here: "you" } }, validator2, (err, result) => {
    console.log(err); // null
    console.log(result); // { name: 'wow' , json: { here: "you" } }
});
```

### Options

* `omitAssociations` : `bool` : omits associated models from the resulting validation schema (`belongsTo` or `hasOne` associations)
* `omitAutoGenerated` : `bool` : omits automatically generated properties from validation schema (like `id`, `createdAt`, and `updatedAt`)
