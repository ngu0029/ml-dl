- [tf.Graph](https://www.tensorflow.org/api_docs/python/tf/Graph)
A TensorFlow computation, represented as a dataflow graph.

A Graph contains a set of tf.Operation objects, which represent units of computation; and tf.Tensor objects, which represent the units of data that flow between operations.

A default Graph is always registered, and accessible by calling tf.get_default_graph. **To add an operation to the default graph, simply call one of the functions that defines a new Operation:**
```
c = tf.constant(4.0)
assert c.graph is tf.get_default_graph()
```
- [tf.Graph().as_default()](https://www.tensorflow.org/api_docs/python/tf/Graph#as_default)

Returns a context manager that makes this Graph the default graph.

This method should be used if you want to create multiple graphs in the same process. For convenience, a global default graph is provided, and all ops will be added to this graph if you do not create a new graph explicitly.

Use this method with the with keyword to specify that ops created within the scope of a block should be added to this graph. In this case, once the scope of the with is exited, the previous default graph is set again as default. There is a stack, so it's ok to have **multiple nested levels of as_default calls**.

**The default graph is a property of the current thread**. If you create a new thread, and wish to use the default graph in that thread, you must explicitly add a with g.as_default(): in that thread's function.
```
# 1. Using Graph.as_default():
g = tf.Graph()
with g.as_default():
  c = tf.constant(5.0)
  assert c.graph is g

# 2. Constructing and making default:
with tf.Graph().as_default() as g: # Using the context manager
  c = tf.constant(5.0)
  assert c.graph is g
```

- [tf.get_default_graph()](https://www.tensorflow.org/api_docs/python/tf/get_default_graph)

Returns the default graph for the current thread.

The returned graph will be the **innermost graph** on which a Graph.as_default() context has been entered, or a **global default graph** if none has been explicitly created.

**NOTE: The default graph is a property of the current thread.** If you create a new thread, and wish to use the default graph in that thread, you must explicitly add a with g.as_default(): in that thread's function.

- [tf.reset_default_graph()](https://www.tensorflow.org/api_docs/python/tf/reset_default_graph)

Clears the default graph stack and resets the global default graph.

**NOTE: The default graph is a property of the current thread.** This function applies only to the current thread. Calling this function while a tf.Session or tf.InteractiveSession is active will result in undefined behavior. Using any previously created tf.Operation or tf.Tensor objects after calling this function will result in undefined behavior.
