# Arabic Root Color Tagging Model - Project Brief

## Project Goal

Build a machine learning model that maps 3-letter Arabic root words to colors in CIE color space. The system will learn associations between letter combinations and color perception through human-labeled training data.

## Approach

- **Model Type**: Linear regression with positional letter weights
- **Training Method**: Manual labeling of word-color pairs via interactive web interface
- **Architecture**: Single integrated application handling both data collection and model training

## Technology Stack

- **Frontend**: HTML5 + JavaScript (vanilla or React)
- **Color Handling**: CIE color space with native/library color picker
- **ML Implementation**: Client-side linear regression (matrix operations)
- **Data Storage**: CSV format with download capability
- **Deployment**: Single-file web application

## Architecture Decision: CSV over JSON

Chosen CSV for structured data storage due to:
- Natural row ordering preservation
- Excel manipulation compatibility
- Simpler file structure for ~8k finite dataset
- Future expansion via column addition
- Reduced parsing overhead

## Implementation Steps

1. **Build Data Collection Interface**
   - Text input for 3-letter words
   - CIE color space picker
   - Training examples display/management
   - Progress tracking

2. **Implement Linear Regression Pipeline**
   - 28×3×3 weight matrix (letters × positions × CIE dimensions)
   - Real-time model training capability
   - Loss calculation and convergence monitoring

3. **Add Prediction Interface**
   - Input field for test words
   - Color prediction display
   - Model performance feedback

4. **Validation Tools**
   - Cross-validation data generation
   - Model evaluation metrics
   - Early stopping indicators

## Model Architecture Details

## Model Architecture Details

**Input Representation**
- Each 3-letter word becomes a sparse vector
- Letter position matters (CAT ≠ TAC)
- 28 possible letters (Arabic alphabet + variations)

**Weight Structure**
```
W[letter][position][color_dimension] = weight value
```

**Prediction Function**
```
For word ABC:
CIE_L = W[A][1][L] + W[B][2][L] + W[C][3][L]
CIE_a = W[A][1][a] + W[B][2][a] + W[C][3][a]  
CIE_b = W[A][1][b] + W[B][2][b] + W[C][3][b]
```

## Plan A: Linear Regression Model

**Input Data Format:**
Each word represented as 3-dimensional vector where each entry is an integer 1-28 (letter indices). Example:
- Word "حمر" → [8, 24, 18] (arbitrary indices for ح, م, ر)
- Indices are purely arbitrary identifiers - all semantic learning happens in weights

**Model Architecture:**
- Weight matrix: 28×3×3 (letters × positions × CIE dimensions)
- No intercept terms (assumes zero-centered color space)
- Linear combination: prediction = sum of relevant weights

**Weight Learning:**
Each (letter, position) pair learns independent contribution to each CIE dimension through gradient descent on prediction error.

## Plan B: Neural Network Architecture  

**Input Data Format:**
One-hot encoding creating 84-dimensional input vector:
- 28 positions for letter 1 (one activated)
- 28 positions for letter 2 (one activated)  
- 28 positions for letter 3 (one activated)
- Example: "حمر" → [0,0,0,0,0,0,0,1,0...0,0,0,1,0...0,0,1,0,0...]

**Network Architecture:**
```
Input Layer: 84 nodes (28×3 one-hot encoding)
Hidden Layer: 3 nodes (semantic feature compression)
Output Layer: 1 node (single CIE dimension)
```

**Weight Learning:**
- Input→Hidden: 84×3 = 252 weights (discovers phonaesthetic patterns)
- Hidden→Output: 3×1 = 3 weights (combines semantic features)
- Hidden nodes learn to represent fundamental phonaesthetic dimensions
- Captures nonlinear letter-position interactions within compressed representation

**Training Multiple Outputs:**
Train 3 separate networks (one per CIE dimension) or modify final layer to output 3 dimensions simultaneously.

## Domain Background

**Arabic Root System**
Arabic words derive from 3-letter roots that carry core semantic meaning. These roots combine in systematic patterns to create related words. Approximately 8,000 theoretical combinations exist, with ~20×20×20 realistic permutations due to linguistic constraints.

**CIE Color Space**
CIE L*a*b* provides perceptually uniform color representation:
- **L*** (Lightness): 0-100, black to white
- **a*** (Green-Red): negative = green, positive = red  
- **b*** (Blue-Yellow): negative = blue, positive = yellow

This 3D space captures human color perception more accurately than RGB, making it ideal for modeling subjective color associations.

**Training Data Requirements**
With ~168 parameters to learn (28×3×3), the rule of thumb suggests 1,680-3,360 training examples. Given ~2,000 available examples, success depends on the underlying systematicity of letter-color relationships.

## Success Criteria

- Convergent loss function during training
- Consistent color predictions for semantically similar roots
- Ability to generalize to unlabeled words
- Intuitive color associations (e.g., fire-related roots → red/orange)