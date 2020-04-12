the schema defines a query.

the query has fields

```
  val Query = ObjectType(
    "Query", 
	fields[CharacterRepo, Unit](
	      Field("hero", Character,
	        arguments = EpisodeArg :: Nil,
	        resolve = (ctx) ⇒ ctx.ctx.getHero(ctx.arg(EpisodeArg)))
	      ,
	      Field("human", OptionType(Human),
	        arguments = ID :: Nil,
	        resolve = ctx ⇒ ctx.ctx.getHuman(ctx arg ID))
	      ,
	      Field("humans", ListType(Human),
	        arguments = LimitArg :: OffsetArg :: Nil,
	        resolve = ctx ⇒ ctx.ctx.getHumans(ctx arg LimitArg, ctx arg OffsetArg))
	)
```


fields is a generic (parameterized) function:

```
  def fields[Ctx, Val](fields: Field[Ctx, Val]*): List[Field[Ctx, Val]] = fields.toList
```
  ie. it takes Field[Ctx, Val]* as arguments


Field is a case class with a resolve field Context[Ctx, Val]:

```
case class Field[Ctx, Val](
    name: String,
    fieldType: OutputType[_],
    description: Option[String],
    arguments: List[Argument[_]],
    resolve: Context[Ctx, Val] ⇒ Action[Ctx, _]
    ...
)
```

Context is another case class:

case class Context[Ctx, Val](
  value: Val,
  ctx: Ctx,
  ...
)


So *calling* `fields[CharacterRepo, Unit](...)` therefore binds Context[Ctx, Val] to Context[CharacterRepo, Val] and therefore the resolve function in each field is bound to type Context[CharacterRepo, Val] => Action[CharacterRepo, _]


