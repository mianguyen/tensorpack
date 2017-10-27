
# Training Interface

Tensorpack trainers provide low-level API which requires a number of options to setup.
There are high-level interfaces built on top of trainer to simplify the use,
when you don't want to customize too much.

### With ModelDesc and TrainConfig

[SingleCost trainers](trainer.html#single-cost-trainers)
expects `InputDesc`, `InputSource`, get_cost function, and optimizer.
`ModelDesc` describes a model by packing three of them together into one object:

```python
class MyModel(ModelDesc):
	def _get_inputs(self):
		return [InputDesc(...), InputDesc(...)]

	def _build_graph(self, inputs):
		tensorA, tensorB = inputs
		# build the graph
		self.cost = xxx	 # define the cost tensor

	def _get_optimizer(self):
	  return tf.train.GradientDescentOptimizer(0.1)
```

`_get_inputs` should define the metainfo of all the inputs your graph may need.
`_build_graph` should add tensors/operations to the graph, where
the argument `inputs` is a list of tensors which will match `_get_inputs`.

You can use any symbolic functions in `_build_graph`, including TensorFlow core library
functions and other symbolic libraries.
But you need to follow the requirement of
[get_cost_fn](http://tensorpack.readthedocs.io/en/latest/modules/train.html#tensorpack.train.SingleCostTrainer.setup_graph),
because this function will be used as part of `get_cost_fn`.
At last you need to set `self.cost`.

After defining such a model, use it with `TrainConfig` and `launch_train_with_config`:

```python
config = TrainConfig(
   model=MyModel()
   dataflow=my_dataflow,
   # data=my_inputsource, # alternatively, use a customized InputSource
   callbacks=[...]
)

trainer = SomeTrainer()
# trainer = SyncMultiGPUTrainerParameterServer([0, 1, 2])
launch_train_with_config(config, trainer)
```
See the docs of
[launch_train_with_config](http://tensorpack.readthedocs.io/en/latest/modules/train.html#tensorpack.train.launch_train_with_config)
for its usage and detailed functionalities.