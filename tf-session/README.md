- [tf.Session()](https://www.tensorflow.org/api_docs/python/tf/Session)

A class for running TensorFlow operations.

A Session object encapsulates the environment in which Operation objects are executed, and Tensor objects are evaluated. For example:
```
# Build a graph.
a = tf.constant(5.0)
b = tf.constant(6.0)
c = a * b

# Launch the graph in a session.
sess = tf.Session()

# Evaluate the tensor `c`.
print(sess.run(c))
```
A session may own resources, such as tf.Variable, tf.QueueBase, and tf.ReaderBase. **It is important to release these resources when they are no longer required. To do this, either invoke the tf.Session.close method on the session, or use the session as a context manager**. The following two examples are equivalent:
```
# Using the `close()` method.
sess = tf.Session()
sess.run(...)
sess.close()

# Using the context manager.
with tf.Session() as sess:
  sess.run(...)
```
```
__init__(
    target='',
    graph=None,
    config=None
)
Creates a new TensorFlow session.
```
If no graph argument is specified when constructing the session, the **default graph** will be launched in the session. If you are using more than one graph (created with tf.Graph() in the same process, you will have to use different sessions for each graph, but each graph can be used in multiple sessions. In this case, it is often clearer to pass the graph to be launched explicitly to the session constructor.

- [tf.Session().as_default()](https://www.tensorflow.org/api_docs/python/tf/Session#as_default)

Returns a context manager that makes this object the default session.

Use with the with keyword to specify that calls to tf.Operation.run or tf.Tensor.eval should be executed in this session.
```
c = tf.constant(..)
sess = tf.Session()

with sess.as_default():
  assert tf.get_default_session() is sess
  print(c.eval())
```  
To get the current default session, use **tf.get_default_session**.

N.B. The **as_default** context manager does not close the session when you exit the context, and you must close the session explicitly.
```
c = tf.constant(...)
sess = tf.Session()
with sess.as_default():
  print(c.eval())
# ...
with sess.as_default():
  print(c.eval())

sess.close()
```
Alternatively, you can use **with tf.Session():** to create a session that is automatically closed on exiting the context, including when an uncaught exception is raised.

N.B. Entering a with sess.as_default(): block does not affect the current default graph. If you are using **multiple graphs**, and sess.graph is different from the value of tf.get_default_graph, you must explicitly enter a with sess.graph.as_default(): block to make sess.graph the default graph.

- [tf.get_default_session()](https://www.tensorflow.org/api_docs/python/tf/get_default_session)

Returns the default session for the current thread.

The returned Session will be the innermost session on which a Session or Session.as_default() context has been entered.

**NOTE: The default session is a property of the current thread**. If you create a new thread, and wish to use the default session in that thread, you must explicitly add a with sess.as_default(): in that thread's function.
