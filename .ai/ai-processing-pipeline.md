# ConnectClass AI Processing Pipeline

## Complete Real-Time Processing Flow

### **Phase 1: Assessment Completion & Scoring (< 1 second)**

```javascript
// Input: Student assessment responses
const assessmentData = {
  studentId: "student_123",
  classId: "class_456", 
  responses: {
    q1: "C", q2: "B", q3: "D", q4: "A", 
    q5: "B", q6: "C", q7: "D", q8: "B",
    q9: "C", q10: "D", q11: "C", q12: "B",
    q13: "I collect vintage train tickets from different countries"
  },
  timestamp: "2024-01-15T10:30:00Z"
};

// Step 1: Convert responses to HEXACO scores
function calculateHEXACOScores(responses) {
  const scoringMatrix = {
    // Each response option maps to 1-4 point scale
    honesty_humility: [
      mapResponseToScore(responses.q1),  // Rare book scenario
      mapResponseToScore(responses.q2)   // Competition scenario
    ],
    emotionality: [
      mapResponseToScore(responses.q3),  // Story finale scenario  
      mapResponseToScore(responses.q4)   // Presentation scenario
    ],
    extraversion: [
      mapResponseToScore(responses.q5),  // Hidden café scenario
      mapResponseToScore(responses.q6)   // Workshop intro scenario
    ],
    agreeableness: [
      mapResponseToScore(responses.q7),  // Collaboration scenario
      mapResponseToScore(responses.q8)   // Community disagreement scenario
    ],
    conscientiousness: [
      mapResponseToScore(responses.q9),  // Collection scenario
      mapResponseToScore(responses.q10)  // Skill mastery scenario
    ],
    openness: [
      mapResponseToScore(responses.q11), // Fusion music scenario
      mapResponseToScore(responses.q12)  // New workshop scenario
    ]
  };
  
  // Average scores per dimension (1-4 scale)
  return Object.entries(scoringMatrix).reduce((profile, [dimension, scores]) => {
    profile[dimension] = scores.reduce((sum, score) => sum + score, 0) / scores.length;
    return profile;
  }, {});
}

// Step 2: Standardize scores for clustering
function standardizeScores(hexacoProfile, classStats) {
  return Object.entries(hexacoProfile).reduce((zScores, [dimension, score]) => {
    const mean = classStats[dimension].mean;
    const stdDev = classStats[dimension].stdDev;
    zScores[dimension] = (score - mean) / stdDev;
    return zScores;
  }, {});
}
```

### **Phase 2: Real-Time Group Formation (< 5 seconds)**

```javascript
// Triggered when teacher clicks "Create Groups" button
async function createStudentGroups(classId) {
  // Step 1: Gather all completed assessments
  const students = await getCompletedAssessments(classId);
  const studentCount = students.length;
  
  if (studentCount < 8) {
    return fallbackGrouping(students); // Rule-based for small classes
  }
  
  // Step 2: Prepare data for FCM clustering
  const hexacoMatrix = students.map(student => [
    student.zScores.honesty_humility,
    student.zScores.emotionality, 
    student.zScores.extraversion,
    student.zScores.agreeableness,
    student.zScores.conscientiousness,
    student.zScores.openness
  ]);
  
  // Step 3: Determine optimal cluster count
  const targetClusters = Math.round(studentCount / 4);
  const clusterRange = [Math.max(2, targetClusters - 1), targetClusters, targetClusters + 1];
  
  let bestClustering = null;
  let bestQuality = Infinity;
  
  // Step 4: Test cluster configurations
  for (const c of clusterRange) {
    const clustering = await fuzzyC Means({
      data: hexacoMatrix,
      clusters: c,
      fuzziness: 2.0,
      maxIterations: 50,
      tolerance: 1e-5
    });
    
    const quality = calculateXieBeniIndex(clustering);
    if (quality < bestQuality) {
      bestQuality = quality;
      bestClustering = clustering;
    }
  }
  
  // Step 5: Quality control check
  if (bestQuality > 0.5) {
    console.log("FCM quality below threshold, using fallback");
    return hierarchicalFallback(students);
  }
  
  // Step 6: Assign students to groups
  const groups = assignStudentsToGroups(students, bestClustering);
  
  return {
    groups: groups,
    qualityIndex: bestQuality,
    algorithm: 'fuzzy_c_means',
    processingTime: Date.now() - startTime,
    compatibility: calculateGroupCompatibility(groups)
  };
}

// Fuzzy C-Means Implementation
async function fuzzyCMeans({ data, clusters, fuzziness, maxIterations, tolerance }) {
  const n = data.length;
  const d = data[0].length; // 6 HEXACO dimensions
  const c = clusters;
  const m = fuzziness;
  
  // Initialize membership matrix randomly
  let U = initializeRandomMembership(n, c);
  let centroids = new Array(c).fill(null).map(() => new Array(d).fill(0));
  
  for (let iter = 0; iter < maxIterations; iter++) {
    const prevU = JSON.parse(JSON.stringify(U));
    
    // Update centroids
    for (let j = 0; j < c; j++) {
      let numerator = new Array(d).fill(0);
      let denominator = 0;
      
      for (let i = 0; i < n; i++) {
        const membership = Math.pow(U[i][j], m);
        denominator += membership;
        for (let k = 0; k < d; k++) {
          numerator[k] += membership * data[i][k];
        }
      }
      
      for (let k = 0; k < d; k++) {
        centroids[j][k] = numerator[k] / denominator;
      }
    }
    
    // Update membership matrix
    for (let i = 0; i < n; i++) {
      for (let j = 0; j < c; j++) {
        let sum = 0;
        const distanceToJ = euclideanDistance(data[i], centroids[j]);
        
        for (let k = 0; k < c; k++) {
          const distanceToK = euclideanDistance(data[i], centroids[k]);
          sum += Math.pow(distanceToJ / distanceToK, 2 / (m - 1));
        }
        
        U[i][j] = 1 / sum;
      }
    }
    
    // Check convergence
    const maxChange = Math.max(...U.flat().map((val, idx) => 
      Math.abs(val - prevU[Math.floor(idx / c)][idx % c])
    ));
    
    if (maxChange < tolerance) {
      console.log(`FCM converged after ${iter + 1} iterations`);
      break;
    }
  }
  
  return { membership: U, centroids: centroids };
}

// Group Assignment with Edge Case Handling
function assignStudentsToGroups(students, clustering) {
  const groups = [];
  const assignments = new Map();
  
  // Assign each student to highest membership cluster
  students.forEach((student, idx) => {
    const memberships = clustering.membership[idx];
    const maxMembership = Math.max(...memberships);
    const clusterIndex = memberships.indexOf(maxMembership);
    
    if (!assignments.has(clusterIndex)) {
      assignments.set(clusterIndex, []);
    }
    assignments.get(clusterIndex).push({
      ...student,
      membership: maxMembership,
      membershipDistribution: memberships
    });
  });
  
  // Handle odd numbers and group size optimization
  let finalGroups = Array.from(assignments.values());
  
  // Move students between groups to optimize for groups of 4
  finalGroups = optimizeGroupSizes(finalGroups);
  
  return finalGroups.map((group, index) => ({
    id: `group_${index + 1}`,
    members: group,
    compatibility: calculateCompatibilityScore(group),
    avgMembership: group.reduce((sum, s) => sum + s.membership, 0) / group.length
  }));
}
```

### **Phase 3: AI Ice-Breaker Generation (< 3 seconds per group)**

```javascript
// Generate group-specific ice-breakers using OpenAI
async function generateGroupIceBreakers(group) {
  const groupProfile = analyzeGroupPersonality(group);
  const sharedInterests = findSharedInterests(group);
  
  const prompt = `Create 3-4 ice-breaker questions for a group of Japanese university students meeting for the first time. 

Group Personality Profile:
- Average Extraversion: ${groupProfile.extraversion}/4 (${groupProfile.extraversion > 2.5 ? 'energetic' : 'moderate'})
- Average Agreeableness: ${groupProfile.agreeableness}/4 (${groupProfile.agreeableness > 2.5 ? 'collaborative' : 'independent'})
- Average Openness: ${groupProfile.openness}/4 (${groupProfile.openness > 2.5 ? 'curious' : 'focused'})

Shared Weird Hobbies/Interests:
${sharedInterests.map(interest => `- ${interest}`).join('\n')}

Requirements:
- Questions should help them discover connections naturally
- Include references to their shared interests when possible
- Suitable for 10-minute face-to-face conversation
- Culturally appropriate for Japanese classroom setting
- Create authentic excitement about getting to know each other

Format as numbered list with brief explanation of why each question works for this specific group.`;

  const response = await openai.chat.completions.create({
    model: "gpt-4",
    messages: [{ role: "user", content: prompt }],
    max_tokens: 500,
    temperature: 0.7
  });
  
  return {
    questions: response.choices[0].message.content,
    groupProfile: groupProfile,
    sharedInterests: sharedInterests,
    generatedAt: new Date().toISOString()
  };
}

// Analyze group personality for ice-breaker customization  
function analyzeGroupPersonality(group) {
  const avgScores = group.reduce((totals, student) => {
    Object.keys(student.hexacoProfile).forEach(dimension => {
      totals[dimension] = (totals[dimension] || 0) + student.hexacoProfile[dimension];
    });
    return totals;
  }, {});
  
  Object.keys(avgScores).forEach(dimension => {
    avgScores[dimension] /= group.length;
  });
  
  return {
    ...avgScores,
    energyLevel: avgScores.extraversion > 2.5 ? 'high' : 'moderate',
    collaborationStyle: avgScores.agreeableness > 2.5 ? 'consensus' : 'direct',
    explorationTendency: avgScores.openness > 2.5 ? 'adventurous' : 'focused'
  };
}

// Find shared interests from open-ended responses
function findSharedInterests(group) {
  const interests = group.map(student => 
    student.responses.q13.toLowerCase().split(/[,\.;]/).map(s => s.trim())
  ).flat();
  
  // Simple keyword matching for common themes
  const themes = ['music', 'art', 'collect', 'game', 'cook', 'travel', 'read', 'write', 'photo'];
  const sharedThemes = themes.filter(theme => 
    interests.filter(interest => interest.includes(theme)).length >= 2
  );
  
  return sharedThemes.length > 0 ? sharedThemes : ['creative hobbies', 'unique interests'];
}
```

### **Phase 4: Error Handling & Fallback Strategies**

```javascript
// Comprehensive fallback system
function handleProcessingErrors(error, students) {
  console.error('AI processing error:', error);
  
  switch (error.type) {
    case 'insufficient_data':
      return createMinimalGroups(students);
      
    case 'clustering_failure':
      return hierarchicalFallback(students);
      
    case 'openai_timeout':
      return useDefaultIceBreakers(students);
      
    case 'network_error':
      return {
        error: 'network_issue',
        message: 'Please check internet connection and try again',
        retryAvailable: true
      };
      
    default:
      return manualGroupingMode(students);
  }
}

// Manual override for teacher control
function enableTeacherOverride(groups) {
  return {
    ...groups,
    manualEditMode: true,
    teacherControls: {
      moveStudent: (studentId, fromGroup, toGroup) => moveStudentBetweenGroups(studentId, fromGroup, toGroup),
      regenerateIceBreaker: (groupId) => generateGroupIceBreakers(getGroupById(groupId)),
      resetGroups: () => createStudentGroups(classId)
    }
  };
}
```

### **Performance Monitoring & Quality Assurance**

```javascript
// Real-time quality monitoring
const performanceMetrics = {
  avgProcessingTime: 0,
  successRate: 0,
  qualityThreshold: 0.5,
  teacherOverrideRate: 0,
  studentSatisfactionScore: 0
};

function logProcessingMetrics(result) {
  // Track performance for optimization
  analytics.track('group_formation_completed', {
    processingTime: result.processingTime,
    qualityIndex: result.qualityIndex,
    algorithm: result.algorithm,
    studentCount: result.groups.reduce((total, group) => total + group.members.length, 0),
    avgCompatibility: result.groups.reduce((total, group) => total + group.compatibility, 0) / result.groups.length
  });
}
```

This pipeline ensures reliable, fast group formation with multiple fallback strategies and quality controls for real classroom use.