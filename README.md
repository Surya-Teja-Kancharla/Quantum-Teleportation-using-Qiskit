<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
</head>
<body>

<h1>Quantum Teleportation with Qiskit</h1>

<p>This repository contains a Jupyter Notebook that demonstrates the <strong>Quantum Teleportation Protocol</strong> using IBM's open-source quantum computing framework, Qiskit. It provides a detailed, step-by-step breakdown of how the protocol works, from creating an entangled pair to recovering the teleported state. The notebook uses both statevector and QASM simulators to illustrate the process and verify its success.</p>

<p>Quantum teleportation is a process that transfers the state of a quantum particle from one location to another, without physically moving the particle itself. It is a cornerstone of quantum information science and a fundamental concept for quantum communication.</p>

<hr>

<h2>Directory Structure</h2>
<pre>
Quantum Teleportation.ipynb
temp.txt
README.md
requirements.txt
</pre>

<hr>

<h2>Notebook Explanation</h2>

<h3>1. Installing Necessary Libraries</h3>
<p>The notebook begins by installing the required Qiskit libraries using a special magic command in Jupyter notebooks. The <code>%pip install</code> command ensures that packages are installed directly into the notebook's Python environment, making them immediately available for use.</p>
<pre><code>%pip install qiskit qiskit-aer qiskit-ibm-provider</code></pre>

<p>The packages installed are:</p>
<ul>
    <li><strong><code>qiskit</code></strong>: Core Quantum SDK for writing circuits, simulating them, and running on real quantum hardware.</li>
    <li><strong><code>qiskit-aer</code></strong>: High-performance local simulator for testing and debugging circuits.</li>
    <li><strong><code>qiskit-ibm-provider</code></strong>: Bridge to IBM Quantum cloud services.</li>
</ul>

<hr>

<h3>2. Importing Libraries</h3>
<p>After installation, the notebook imports several modules to build the quantum teleportation circuit.</p>
<pre><code>import numpy as np
from qiskit import QuantumCircuit, QuantumRegister, ClassicalRegister, transpile
from qiskit_aer import AerSimulator
from qiskit.visualization import plot_histogram, plot_bloch_multivector, array_to_latex
from qiskit.circuit.library import Initialize, XGate, ZGate
from qiskit.quantum_info import random_statevector</code></pre>

<p>Key imports explained:</p>
<ul>
    <li><code>numpy as np</code>: For numerical computations and quantum state manipulation.</li>
    <li><code>QuantumCircuit</code>: Represents a series of quantum gates and qubits.</li>
    <li><code>QuantumRegister</code> & <code>ClassicalRegister</code>: Containers for qubits and classical bits.</li>
    <li><code>transpile</code>: Optimizes and restructures circuits for hardware or simulator.</li>
    <li><code>AerSimulator</code>: Local simulator to run circuits.</li>
    <li><code>plot_bloch_multivector</code>: Visualizes a qubit's state on the Bloch sphere.</li>
    <li><code>random_statevector</code>: Generates a random 1-qubit quantum state.</li>
    <li><code>Initialize</code>: Prepares a qubit in a specific state vector.</li>
    <li><code>XGate</code> and <code>ZGate</code>: Pauli-X (NOT) and Pauli-Z (phase flip) gates for corrections.</li>
</ul>

<hr>

<h3>3. Helper Functions</h3>
<p>The notebook defines helper functions to modularize the teleportation protocol.</p>
<pre><code># Define helper functions
def create_bell_pair(qc, a, b):
    """Creates a Bell pair in qc using qubits a & b"""
    qc.h(a)
    qc.cx(a, b)

def alice_gates(qc, psi, a):
    qc.cx(psi, a)
    qc.h(psi)

def measure_and_send(qc, a, b):
    """Measures qubits a & b"""
    qc.barrier()
    qc.measure(a, 0)
    qc.measure(b, 1)

def bob_gates(qc, qubit, crz, crx):
    """
    Applies conditional gates to the qubit based on classical bits
    """
    with qc.if_test((crx, 1)):
        qc.x(qubit)
    with qc.if_test((crz, 1)):
        qc.z(qubit)

def new_bob_gates(qc, a, b, c):
    """Alternative implementation using controlled gates"""
    qc.cx(b, c)
    qc.cz(a, c)
</code></pre>

<ul>
    <li><strong>create_bell_pair(qc, a, b)</strong>: Creates a maximally entangled Bell pair between two qubits.</li>
    <li><strong>alice_gates(qc, psi, a)</strong>: Alice applies CNOT and Hadamard gates to entangle the message qubit with her Bell qubit.</li>
    <li><strong>measure_and_send(qc, a, b)</strong>: Alice measures her qubits and sends classical outcomes to Bob.</li>
    <li><strong>bob_gates(qc, qubit, crz, crx)</strong>: Bob applies X/Z gates conditionally to reconstruct the teleported state.</li>
    <li><strong>new_bob_gates(qc, a, b, c)</strong>: Alternative, more compact implementation using controlled gates.</li>
</ul>

<hr>

<h3>4. Circuit Construction and Simulation</h3>

<h4>4.1. Generating and Initializing the Random State</h4>
<p>A random one-qubit quantum state <code>psi</code> is generated using <code>random_statevector(2)</code>. Its mathematical representation is displayed and visualized on the Bloch sphere.</p>
<pre><code># Generate random state
psi = random_statevector(2)
display(array_to_latex(psi.data, prefix='\\psi ='))
plot_bloch_multivector(psi)</code></pre>

<h4>4.2. Creating the Bell Pair</h4>
<p>The Bell pair is created between Alice's second qubit (<code>qr[1]</code>) and Bob's qubit (<code>qr[2]</code>). A barrier separates this step visually.</p>
<pre><code># Step 1: Create Bell pair
create_bell_pair(qc, 1, 2)
qc.barrier()
qc.draw('mpl')</code></pre>

<h4>4.3. Alice's Entanglement and Measurement</h4>
<p>Alice entangles her message qubit with her Bell qubit and then measures both qubits. The outcomes are stored in classical bits for Bob to use.</p>
<pre><code># Step 2: Alice's gates
alice_gates(qc, 0, 1)
qc.draw('mpl')

  
\# Step 3: Measure and send
measure_and_send(qc, 0, 1)
qc.draw('mpl')</code></pre>


<h4>4.4. Bob's Corrections and Verification</h4>
<p>Bob applies corrections using Alice's classical bits. The inverse of the initialization gate is applied to verify successful teleportation.</p>
<pre><code># Step 4: Bob applies gates
bob_gates(qc, 2, crz, crx)
qc.draw('mpl')</code></pre>

<hr>

<h3>5. Simulation Results</h3>
<p>The circuit is run on a QASM simulator for 1024 shots. The histogram and counts show successful teleportation.</p>
<pre><code># Run on QASM simulator
sim = AerSimulator()
t_qc = transpile(qc, sim)
t_qc.save_statevector()
result = sim.run(t_qc, shots=1024).result()

counts = result.get_counts()
plot_histogram(counts)

print(result.get_counts())
# Output example:
# {'0 0 1': 245, '0 0 0': 271, '0 1 0': 261, '0 1 1': 247}</code></pre>

<p>Each key in the output represents a 3-bit string corresponding to <code>cr_result</code> (Bob's final measurement), <code>crx</code>, and <code>crz</code> (Alice's measurement outcomes). Bob's qubit consistently being 0 confirms successful teleportation, while varying <code>crx</code> and <code>crz</code> show Alice's measurement randomness used for correction.</p>

<hr>

</body>
</html>
